---
title: O que é o Gerenciador de Dados Blockchain para o Azure Blockchain Service?
description: O Gerenciador de Dados de Blockchain captura, transforma e entrega dados de blockchain para tópicos da Grade de Eventos.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: chroyal
ms.openlocfilehash: 78c93880007259267b26cf53e93de722be1c7323
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "77209436"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>O que é o Gerenciador de Dados Blockchain para o Azure Blockchain Service?

O Gerenciador de Dados de Blockchain captura, transforma e entrega dados de transação do Azure Blockchain Service para Tópicos da Grade de Eventos do Azure, fornecendo integração escalonável e confiável do razão de blockchain com os serviços do Azure.

Na maioria dos cenários de blockchain empresarial, um razão de blockchain é parte de uma solução. Por exemplo, para transferir um ativo de uma entidade para outra, você precisa de um mecanismo para enviar a transação. Em seguida, você precisará de um mecanismo para ler os dados do razão a fim de garantir que a transação tenha ocorrido, sido aceita e que as alterações de estado resultantes sejam integradas à sua solução de ponta a ponta. Neste exemplo, se gravar um contrato inteligente para transferir ativos, você poderá usar Gerenciador de Dados de Blockchain para integrar aplicativos e armazenamentos de dados não pertencentes à cadeia. Para o exemplo de transferência de ativos, quando um ativo for transferido no blockchain, os eventos e as alterações de estado de propriedade serão entregues pelo Gerenciador de Dados de Blockchain por meio da Grade de Eventos. Então, você poderá usar vários manipuladores de eventos possíveis para a Grade de Eventos armazenar dados do blockchain fora da cadeia ou reagir às alterações de estado em tempo real.

O Gerenciador de Dados de Blockchain executa três funções principais: capturar, transformar e entregar.

![Funções do Gerenciador de Dados de Blockchain](./media/data-manager/functions.png)

## <a name="capture"></a>Capturar

Cada instância do Gerenciador de Dados de Blockchain se conecta a um nó de transação membro do Azure Blockchain Service. Somente os usuários com acesso ao nó de transação podem criar uma conexão garantindo o controle de acesso apropriado aos dados do cliente. Uma instância do Gerenciador de Dados de Blockchain captura de forma confiável todos os dados brutos de bloco e transação do nó de transação e pode redimensionar para dar suporte a cargas de trabalho empresariais.

## <a name="transform"></a>Transformar

Você pode usar o Gerenciador de Dados de Blockchain para decodificar eventos e o estado da propriedade configurando aplicativos de contrato inteligente dentro do Gerenciador de Dados de Blockchain. Para adicionar um contrato inteligente, você fornece a ABI e o código de bytes do contrato. O Gerenciador de Dados de Blockchain usa os artefatos do contrato inteligente para decodificar e descobrir endereços de contrato. Depois de adicionar o aplicativo de blockchain à instância, o Gerenciador de Dados de Blockchain descobre dinamicamente o endereço do contrato inteligente quando o contrato inteligente é implantado no consórcio e envia o evento e o estado da propriedade decodificados aos destinos configurados.

## <a name="deliver"></a>Fornecimento

O Gerenciador de Dados de Blockchain é compatível com várias conexões de saída aos Tópico da Grade de Eventos para qualquer instância especificada do Gerenciador de Dados de Blockchain. Você pode enviar dados de blockchain para um único destino ou para vários destinos. Usando o Gerenciador de Dados de Blockchain, você pode criar uma solução escalonável de publicação de dados baseada em eventos para qualquer implantação de Blockchain.

## <a name="configuration-options"></a>Opções de configuração

Você pode configurar o Gerenciador de Dados de Blockchain para atender às necessidades da sua solução. Por exemplo, você pode provisionar:

* Uma única instância do Gerenciador de Dados de Blockchain para um membro do Azure Blockchain Service.
* Uma instância do Gerenciador de Dados de Blockchain por nó de transação do Azure Blockchain Service. Por exemplo, os nós de transação particulares podem ter sua própria instância do Gerenciador de Dados de Blockchain para manter a confidencialidade.
* Uma instância do Gerenciador de Dados de Blockchain é compatível com várias conexões de saída. Uma instância do Gerenciador de Dados de Blockchain pode ser usada para gerenciar todos os pontos de integração de publicação de dados para um membro do Azure Blockchain Service.

## <a name="next-steps"></a>Próximas etapas

Experimente [criar uma instância do Gerenciador de Dados de Blockchain](data-manager-portal.md) para um membro do Azure Blockchain Service.
