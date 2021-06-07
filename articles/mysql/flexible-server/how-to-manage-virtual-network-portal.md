---
title: Gerenciar redes virtuais-portal do Azure-banco de dados do Azure para MySQL-servidor flexível
description: Criar e gerenciar redes virtuais para o banco de dados do Azure para MySQL-servidor flexível usando o portal do Azure
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 4906ce9f562910f0a087cd25167457ec1fb301ec
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109978"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Criar e gerenciar redes virtuais para o banco de dados do Azure para MySQL-servidor flexível usando o portal do Azure

> [!IMPORTANT]
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

O servidor flexível do banco de dados do Azure para MySQL é compatível com tipos de métodos de conectividade de rede mutuamente exclusivos, para se conectar ao seu servidor flexível. As duas opções são:

- Acesso público (endereços IP permitidos)
- Acesso privado (Integração VNet)

Neste artigo, vamos nos concentrar na criação do MySQL Server com **acesso privado (integração VNet)** usando portal do Azure. Com o acesso privado (integração VNet), você pode implantar seu servidor flexível em sua própria [rede virtual do Azure](../../virtual-network/virtual-networks-overview.md). As redes virtuais do Azure fornecem comunicação de rede privada e segura. Com o acesso privado, as conexões com o servidor MySQL são restritas à sua rede virtual. Para saber mais sobre isso, consulte [acesso privado (integração VNet)](./concepts-networking.md#private-access-vnet-integration).

Você pode implantar seu servidor flexível em uma rede virtual e em uma sub-rede durante a criação do servidor. Depois que o servidor flexível for implantado, você não poderá movê-lo para outra rede virtual, sub-rede ou para *acesso público (endereços IP permitidos)* .

## <a name="prerequisites"></a>Pré-requisitos
Para criar um servidor flexível em uma rede virtual, você precisa de:
- Uma [rede virtual](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > A rede virtual e a sub-rede devem estar na mesma região e assinatura que o servidor flexível.

-  Para [delegar uma sub-rede](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) para **Microsoft. DBforMySQL/flexibleServers**. Essa delegação significa que somente os servidores flexíveis do banco de dados do Azure para MySQL podem usar essa sub-rede. Nenhum outro tipo de recurso do Azure pode estar na sub-rede delegada.

## <a name="create-azure-database-for-mysql-flexible-server-in-an-already-existing-virtual-network"></a>Criar um servidor flexível do banco de dados do Azure para MySQL em uma rede virtual já existente

1. Selecione **Criar um recurso** (+) no canto superior esquerdo do portal.
2. Selecione **Bancos de Dados** > **Banco de Dados do Azure para MySQL**. Você também pode inserir **MySQL** na caixa de pesquisa para localizar o serviço.
3. Selecione **Servidor flexível** como a opção de implantação.
4. Preencha o formulário **básico** .
5. Vá para a guia **rede** para configurar o modo como você deseja se conectar ao servidor.
6. No **método de conectividade**, selecione **acesso privado (integração VNet)**. Vá para **rede virtual** e selecione a *rede virtual* já existente e a *sub-rede* criada como parte dos pré-requisitos acima.
7. Selecione **Examinar + criar** para examinar a configuração do servidor flexível.
8. Selecione **Criar** para provisionar o servidor. O provisionamento pode levar alguns minutos.

>[!Note]
> Depois que o servidor flexível for implantado em uma rede virtual e sub-rede, você não poderá movê-lo para acesso público (endereços IP permitidos).

## <a name="next-steps"></a>Próximas etapas
- [Criar e gerenciar uma rede virtual do servidor flexível do banco de dados do Azure para MySQL usando o CLI do Azure](./how-to-manage-virtual-network-cli.md).
- Saiba mais sobre a [rede no banco de dados do Azure para MySQL servidor flexível](./concepts-networking.md)
- Saiba mais sobre [a rede virtual do banco de dados do Azure para MySQL servidor flexível](./concepts-networking.md#private-access-vnet-integration).
