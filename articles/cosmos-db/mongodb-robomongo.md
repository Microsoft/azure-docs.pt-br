---
title: Usar o Robomongo no Azure Cosmos DB | Microsoft Docs
description: 'Saiba como usar o Robomongo com uma conta do Azure Cosmos DB: API para MongoDB'
keywords: robomongo
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: anhoh
ms.openlocfilehash: 8983594776a1bbe413a6d7cf2cd518f0e327648a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>Usar o Robomongo com uma conta do Azure Cosmos DB: API para MongoDB
Para se conectar a uma conta do Azure Cosmos DB: API para MongoDB usando o Robomongo, é necessário:

* Baixar e instalar o [Robomongo](https://robomongo.org/)
* Ter as informações de [cadeia de conexão](connect-mongodb-account.md) de sua conta do Azure Cosmos DB: API para MongoDB

## <a name="connect-using-robomongo"></a>Conectar usando o Robomongo
Para adicionar sua conta do Azure Cosmos DB: API para MongoDB às Conexões entre o Robomongo e o MongoDB, realize as etapas a seguir.

1. Recupere as informações de conexão de sua conta do Azure Cosmos DB: API para MongoDB usando as instruções descritas [aqui](connect-mongodb-account.md).

    ![Captura de tela da folha de cadeia de conexão](./media/mongodb-robomongo/connectionstringblade.png)
2. Execute *Robomongo.exe*

3. Clique no botão de conexão em **Arquivo** para gerenciar suas conexões. Em seguida, clique em **Criar** na janela **Conexões do MongoDB**, o que abrirá a janela **Configurações de Conexão**.

4. No janela **Configurações de Conexão**, escolha um nome. Em seguida, localize o **Host** e **Porta** de suas informações de conexão na Etapa 1 e insira-os em **Endereço** e **Porta**, respectivamente.

    ![Captura de tela do Robomongo Manage Connections](./media/mongodb-robomongo/manageconnections.png)
5. Na guia **Autenticação**, clique em **Executar autenticação**. Em seguida, insira seu Banco de dados (o padrão é *Admin*), **Nome de Usuário** e **Senha**.
Tanto o **Nome de Usuário** quanto a **Senha** podem ser encontrados em suas informações de conexão na Etapa 1.

    ![Captura de tela da guia de Autenticação do Robomongo](./media/mongodb-robomongo/authentication.png)
6. Na guia **SSL**, marque **Usar protocolo SSL** e altere o **Método de Autenticação** para **Certificado Autoassinado**.

    ![Captura de tela da guia Robomongo SSL](./media/mongodb-robomongo/SSL.png)
7. Por fim, clique em **Teste** para verificar se você é capaz de se conectar e, em seguida, **Salve**.

## <a name="next-steps"></a>Próximas etapas
* Conheça as [amostras](mongodb-samples.md) do Azure Cosmos DB: API para MongoDB.
