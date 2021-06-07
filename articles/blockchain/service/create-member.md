---
title: Criar um membro do Azure Blockchain Service – portal do Azure
description: Crie um membro do Azure Blockchain Service para um consórcio Blockchain usando o portal do Azure.
ms.reviewer: ravastra
ms.date: 07/16/2020
ms.topic: quickstart
ms.custom:
- references_regions
- mode-portal
ms.openlocfilehash: 9fe8b7856a99930c16b4173e432fa0b30642582c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536195"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Início Rápido: Criar um membro do blockchain do Azure Blockchain Service usando o portal do Azure

Neste início rápido, você implanta um novo membro do blockchain e o consórcio no Azure Blockchain Service usando o portal do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Nenhum.

## <a name="create-a-blockchain-member"></a>Criar um membro do blockchain

Um membro do Azure Blockchain Service é um nó de blockchain em uma rede privada de blockchain de consórcio. Ao provisionar um membro, você pode criar uma rede de consórcio ou ingressar nela. Você precisará de, pelo menos, um membro para uma rede de consórcio. O número de membros de blockchain necessários para os participantes depende do cenário. Os participantes do consórcio podem ter um ou mais membros de blockchain ou podem compartilhar membros com outros participantes. Para obter mais informações sobre os consórcios, confira [Consórcio do Azure Blockchain Service](consortium.md).

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Selecione **Blockchain** > **Azure Blockchain Service (versão prévia)** .

    ![Criar Serviço](./media/create-member/create-member.png)

    Configuração | Descrição
    --------|------------
    Subscription | Selecione a assinatura do Azure que deseja usar para o serviço. Se você tem várias assinaturas, escolha a assinatura na qual recebe a cobrança do recurso.
    Resource group | Crie um novo nome do grupo de recursos ou escolha um em sua assinatura.
    Região | Escolha uma região para criar o membro. Todos os membros do consórcio devem estar na mesma localização. Os recursos podem não estar disponíveis em algumas regiões. O Gerenciador de Dados do Azure Blockchain está disponível atualmente nas seguintes regiões do Azure: Leste dos EUA e Europa Ocidental.
    Protocolo | No momento, a versão prévia do Azure Blockchain Service é compatível com o protocolo Quorum.
    Consórcio | Para um novo consórcio, insira um nome exclusivo. Se estiver ingressando em um consórcio por meio de um convite, escolha o consórcio no qual você está ingressando. Para obter mais informações sobre os consórcios, confira [Consórcio do Azure Blockchain Service](consortium.md).
    Nome | Escolha um nome exclusivo para o membro do Azure Blockchain Service. O nome do membro do blockchain só pode conter letras minúsculas e números. O primeiro caractere precisa ser uma letra. O valor precisa ter entre 2 e 20 caracteres.
    Senha da conta de membro | A senha da conta do membro é usada para criptografar a chave privada para a conta do Ethereum criada para o membro. Use a conta do membro e a senha da conta do membro para o gerenciamento do consórcio.
    Preços | A configuração e o custo de nó do novo serviço. Selecione o link **Alterar** para escolher entre as camadas de serviço **Standard** e **Basic**. Use a camada *Basic* para desenvolvimento, teste e prova de conceitos. Use a camada *Standard* para implantações de nível de produção. Use também a camada *Standard* se estiver usando o Gerenciador de Dados do Blockchain ou enviando um alto volume de transações particulares. Não há suporte para a alteração do tipo de preço entre Básico e Standard após a criação do membro.
    Senha do nó | A senha do nó de transação padrão do membro. Use a senha para a autenticação básica ao se conectar ao ponto de extremidade público do nó de transação padrão do membro do blockchain.

1. Selecione **Examinar + criar** para validar suas configurações. Selecione **Criar** para provisionar o serviço. O provisionamento leva cerca de 10 minutos.
1. Selecione **Notificações** na barra de ferramentas para monitorar o processo de implantação.
1. Após a implantação, navegue para o membro do blockchain.

Selecione **Visão Geral** e exiba as informações básicas sobre seu serviço, incluindo o endereço do RootContract e a conta de membro.

![Visão geral do membro do blockchain](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode usar o membro criado para o próximo Início Rápido ou tutorial. Quando eles não forem mais necessários, você poderá excluir os recursos excluindo o grupo de recursos `myResourceGroup` criado para o início rápido.

Para excluir o grupo de recursos:

1. No portal do Azure, navegue até **Grupo de recursos** no painel de navegação esquerdo e selecione o grupo de recursos que você deseja excluir.
2. Selecione **Excluir grupo de recursos**. Verifique a exclusão digitando o nome do grupo de recursos e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um membro do Azure Blockchain Service e um novo consórcio. Experimente o próximo início rápido para usar o Azure Blockchain Development Kit for Ethereum para fazer a anexação a um membro do Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Usar o Visual Studio Code para se conectar ao Azure Blockchain Service](connect-vscode.md)
