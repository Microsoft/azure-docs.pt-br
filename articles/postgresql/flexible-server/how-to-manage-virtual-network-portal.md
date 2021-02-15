---
title: Gerenciar redes virtuais-portal do Azure-banco de dados do Azure para PostgreSQL-servidor flexível
description: Criar e gerenciar redes virtuais para o banco de dados do Azure para PostgreSQL – servidor flexível usando o portal do Azure
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 46d8fe6427b2a3e7811719792ac4bf67ddbcc3c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933731"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Criar e gerenciar redes virtuais para o banco de dados do Azure para PostgreSQL – servidor flexível usando o portal do Azure

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Banco de dados do Azure para PostgreSQL-o servidor flexível dá suporte a dois tipos de métodos de conectividade de rede mutuamente exclusivos para se conectar ao seu servidor flexível. As duas opções são:

* Acesso público (endereços IP permitidos)
* Acesso privado (Integração VNet)

Neste artigo, vamos nos concentrar na criação do servidor PostgreSQL com **acesso privado (integração VNet)** usando portal do Azure. Com o acesso privado (integração VNet), você pode implantar seu servidor flexível em sua própria [rede virtual do Azure](../../virtual-network/virtual-networks-overview.md). As redes virtuais do Azure fornecem comunicação de rede privada e segura. Com o acesso privado, as conexões com o servidor PostgreSQL são restritas à sua rede virtual. Para saber mais sobre isso, consulte [acesso privado (integração VNet)](./concepts-networking.md#private-access-vnet-integration).

Você pode implantar seu servidor flexível em uma rede virtual e em uma sub-rede durante a criação do servidor. Depois que o servidor flexível for implantado, você não poderá movê-lo para outra rede virtual, sub-rede ou para *acesso público (endereços IP permitidos)* .

## <a name="prerequisites"></a>Pré-requisitos
Para criar um servidor flexível em uma rede virtual, você precisa de:
- Uma [rede virtual](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > A rede virtual e a sub-rede devem estar na mesma região e assinatura que o servidor flexível.

-  Para [delegar uma sub-rede](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) para **Microsoft. DBforPostgreSQL/flexibleServers**. Essa delegação significa que somente os servidores flexíveis do banco de dados do Azure para PostgreSQL podem usar essa sub-rede. Nenhum outro tipo de recurso do Azure pode estar na sub-rede delegada.

## <a name="create-azure-database-for-postgresql---flexible-server-in-an-already-existing-virtual-network"></a>Criar banco de dados do Azure para PostgreSQL-servidor flexível em uma rede virtual já existente

1. Selecione **Criar um recurso** (+) no canto superior esquerdo do portal.
2. Selecione **Bancos de Dados** > **Banco de Dados do Azure para PostgreSQL**. Você também pode inserir **PostgreSQL** na caixa de pesquisa para localizar o serviço.
3. Selecione **Servidor flexível** como a opção de implantação.
4. Preencha o formulário **básico** .
5. Vá para a guia **rede** para configurar o modo como você deseja se conectar ao servidor.
6. No **método de conectividade**, selecione **acesso privado (integração VNet)**. Vá para **rede virtual** e selecione a *rede virtual* já existente e a *sub-rede* criada como parte dos pré-requisitos acima.
7. Selecione **Examinar + criar** para examinar a configuração do servidor flexível.
8. Selecione **Criar** para provisionar o servidor. O provisionamento pode levar alguns minutos.

>[!Note]
> Depois que o servidor flexível for implantado em uma rede virtual e sub-rede, você não poderá movê-lo para acesso público (endereços IP permitidos).
## <a name="next-steps"></a>Próximas etapas
- [Crie e gerencie o banco de dados do Azure para PostgreSQL – rede virtual de servidor flexível usando o CLI do Azure](./how-to-manage-virtual-network-cli.md).
- Saiba mais sobre [rede no banco de dados do Azure para PostgreSQL – servidor flexível](./concepts-networking.md)
- Saiba mais sobre o [banco de dados do Azure para PostgreSQL – rede virtual de servidor flexível](./concepts-networking.md#private-access-vnet-integration).