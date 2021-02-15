---
title: Consórcio do serviço Blockchain do Azure
description: Visão geral de como o serviço Blockchain do Azure implementa redes Blockchain do Consortium.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84712523"
---
# <a name="azure-blockchain-service-consortium"></a>Consórcio do serviço Blockchain do Azure

Usando o serviço Blockchain do Azure, você pode criar redes Blockchain do consórcio privadas em que cada rede Blockchain pode ser limitada a participantes específicos na rede. Somente os participantes na rede particular do consórcio blockchain podem exibir e interagir com o blockchain. As redes Consortium no serviço Blockchain do Azure podem conter dois tipos de funções de participante de membro:

* Participantes com privilégios de **administrador** que podem tomar ações de gerenciamento de consórcio e podem participar de transações de blockchain.

* **Usuários** -participantes que não podem realizar nenhuma ação de gerenciamento de consórcio, mas podem participar de transações de blockchain.

As redes Consortium podem ser uma combinação de funções de participante e podem ter um número arbitrário de cada tipo de função. Deve haver pelo menos um administrador.

O diagrama a seguir mostra uma rede Consortium com vários participantes:

![Diagrama de rede do consórcio privado](./media/consortium/network-diagram.png)

Com o gerenciamento de consórcio no serviço Blockchain do Azure, você pode gerenciar os participantes na rede do consórcio. O gerenciamento do consórcio é baseado no modelo de consenso da rede. Na versão de visualização atual, o serviço de Blockchain do Azure fornece um modelo de consenso centralizado para o gerenciamento do consórcio. Qualquer participante privilegiado com uma função de administração pode tomar ações de gerenciamento de consórcio, como adicionar ou remover participantes de uma rede.

## <a name="roles"></a>Funções

Os participantes de um consórcio podem ser indivíduos ou organizações e podem ser atribuídos a uma função de usuário ou uma função de administrador. A tabela a seguir lista as diferenças de alto nível entre as duas funções:

| Ação | Função de usuário | Função de administrador
|--------|:----:|:------------:|
| Criar novo membro | Sim | Sim |
| Convidar novos membros | Não | Sim |
| Definir ou alterar a função de participante do membro | Não | Sim |
| Alterar nome de exibição do membro | Somente para o próprio membro | Somente para o próprio membro |
| Remover membros | Somente para o próprio membro | Sim |
| Participar de transações de blockchain | Sim | Sim |

### <a name="user-role"></a>Função de usuário

Os usuários são participantes do consórcio sem recursos de administrador. Eles não podem participar do gerenciamento de membros relacionados ao consórcio. Os usuários podem alterar seu nome de exibição de membro e podem se remover de um consórcio.

### <a name="administrator"></a>Administrador

Um administrador pode gerenciar Membros no consórcio. Um administrador pode convidar Membros, remover membros ou atualizar funções de membros dentro do consórcio.
Sempre deve haver pelo menos um administrador em um consórcio. O último administrador deve especificar outro participante como uma função de administrador antes de sair de um consórcio.

## <a name="managing-members"></a>Gerenciando Membros

Somente os administradores podem convidar outros participantes para o consórcio. Os administradores convidam participantes usando sua ID de assinatura do Azure.

Uma vez convidado, os participantes podem ingressar no blockchain Consortium implantando um novo membro no serviço Blockchain do Azure. Para exibir e ingressar no consórcio convidado, você deve especificar a mesma ID de assinatura do Azure usada no convite pelo administrador de rede.

Os administradores podem remover qualquer participante do consórcio, incluindo outros administradores. Os membros só podem se remover de um consórcio.

## <a name="consortium-management-smart-contract"></a>Contrato inteligente de gerenciamento do consórcio

O gerenciamento de consórcio no serviço Blockchain do Azure é feito por meio de contratos inteligentes de gerenciamento de consórcio. Os contratos inteligentes são implantados automaticamente em seus nós quando você implanta um novo membro blockchain.

O endereço do contrato inteligente de gerenciamento do consórcio raiz pode ser exibido no portal do Azure. O **endereço RootContract** está na seção visão geral do membro do blockchain.

![Endereço RootContract](./media/consortium/rootcontract-address.png)

Você pode interagir com o contrato inteligente de gerenciamento do consórcio usando o [módulo do PowerShell](manage-consortium-powershell.md)de gerenciamento do consórcio, portal do Azure ou diretamente por meio do contrato inteligente usando a conta Ethereum gerada pelo serviço Blockchain do Azure.

## <a name="ethereum-account"></a>Conta do Ethereum

Quando um membro é criado, uma chave de conta do Ethereum é criada. O serviço Blockchain do Azure usa a chave para criar transações relacionadas ao gerenciamento do consórcio. A chave de conta do Ethereum é gerenciada pelo serviço de Blockchain do Azure automaticamente.

A conta de membro pode ser exibida no portal do Azure. A conta de membro está na seção de visão geral do membro do blockchain.

![Conta de membro](./media/consortium/member-account.png)

Você pode redefinir sua conta do Ethereum clicando em sua conta de membro e inserindo uma nova senha. O endereço da conta do Ethereum e a senha serão redefinidos.  

## <a name="next-steps"></a>Próximas etapas

As ações de gerenciamento do Consórcio podem ser acessadas por meio do PowerShell. Para obter mais informações, consulte [gerenciar membros do Consortium no serviço Blockchain do Azure usando o PowerShell](manage-consortium-powershell.md).
