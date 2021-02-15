---
title: Usar Azure Data Studio para gerenciar sua instância do PostgreSQL
description: Usar Azure Data Studio para gerenciar sua instância do PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7dcc0f916a15598060e034dcf62536ee13e2672e
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320229"
---
# <a name="use-azure-data-studio-to-manage-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Use Azure Data Studio para gerenciar seu grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc


Este artigo descreve como:
- gerencie suas instâncias do PostgreSQL com exibições de painel, como visão geral, cadeias de conexão, Propriedades Resource Health...
- trabalhe com seus dados e esquema

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Instalar azdata, Azure Data Studio e CLI do Azure](install-client-tools.md)
- Instalar em Azure Data Studio as **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** extensões Arc e **PostgreSQL** **do Azure**
- Criar o [controlador de dados de arco do Azure](create-data-controller-using-azdata.md)
- Iniciar Azure Data Studio

## <a name="connect-to-the-azure-arc-data-controller"></a>Conectar-se ao controlador de dados de arco do Azure

Em Azure Data Studio, expanda o nó **controladores de arco do Azure** e selecione o botão **conectar controlador** :

Insira as informações de conexão para o controlador de dados do Azure:

- **URL do controlador:**

    A URL para se conectar ao controlador no kubernetes. Inserido na forma de `https://<IP_address_of_the_controller>:<Kubernetes_port.` por exemplo:

    ```console
    https://12.345.67.890:30080
    ```
- **Nome de usuário:**

    Nome da conta de usuário que você usa para se conectar ao controlador. Use o nome que você normalmente usa ao executar `azdata login` . Não é o nome do usuário PostgreSQL que você usa para se conectar ao mecanismo de banco de dados PostgreSQL normalmente em psql.
- **Senha:** A senha da conta de usuário que você usa para se conectar ao controlador


O Azure Data Studio mostra seu controlador de dados de arco. Expanda-o e ele mostra a lista de instâncias do PostgreSQL que ele gerencia.

## <a name="manage-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Gerenciar seus grupos de servidores de hiperescala PostgreSQL habilitados para o Azure Arc

Clique com o botão direito do mouse na instância do PostgreSQL que você deseja gerenciar e selecione [gerenciar]

A exibição do painel PostgreSQL:

Isso apresenta vários painéis listados no lado esquerdo do painel:

- **Visão geral:** Exibe informações de resumo sobre sua instância, como nome, nome de usuário do administrador do PostgreSQL, ID da assinatura do Azure, configuração, versão do mecanismo de banco de dados, pontos de extremidade para Grafana e Kibana...
- **Cadeias de conexão:** Exibe várias cadeias de conexão que podem ser necessárias para se conectar à instância do PostgreSQL, como psql, Node.js, PHP, Ruby...
- **Diagnosticar e resolver problemas:** É a página de aterrissagem na qual você encontrará vários recursos que ajudarão você a solucionar problemas de sua instância enquanto expandimos os notebooks de solução de problemas
- **Nova solicitação de suporte:** É a página de aterrissagem da qual você poderá solicitar assistência de nossos serviços de suporte iniciando o anúncio de visualização pública.

## <a name="work-with-your-data-and-schema"></a>Trabalhe com seus dados e esquema

No lado esquerdo da janela Azure Data Studio, expanda os **servidores**de nó:

E selecione [Adicionar conexão] e preencha os detalhes da conexão com a instância do PostgreSQL:
- **Tipo de conexão:** PostgreSQL
- **Nome do servidor:** Insira o nome da instância do PostgreSQL. Por exemplo: postgres01
- **Tipo de autenticação:** La
- **Nome de usuário:** por exemplo, você pode usar o nome de usuário administrador do PostgreSQL padrão/padrão. Observe que esse campo diferencia maiúsculas de minúsculas.
- **Senha:** você encontrará a senha do nome de usuário PostgreSQL na cadeia de conexão psql na saída do `azdata postgres server endpoint -n postgres01` comando
- **Nome do banco de dados:** defina o nome do banco de dados ao qual você deseja se conectar. Você pode deixá-lo definido como __padrão__
- **Grupo de servidores:** você pode deixá-lo definido como __padrão__
- **Nome (opcional):** você pode deixá-lo em branco
- **Avançadas**
    - **Endereço IP do host:** é o endereço IP público do cluster kubernetes
    - **Porta:** é a porta na qual sua instância do PostgreSQL está ouvindo. Você pode encontrar essa porta no final da cadeia de conexão psql na saída do `azdata postgres server endpoint -n postgres01` comando. Não porta 30080 na qual o kubernetes está ouvindo e que você inseriu ao conectar-se ao controlador de dados do Azure no Azure Data Studio.
    - **Outros parâmetros:** Eles devem ser autoexplícitos, você pode viver com os valores padrão/em branco com os quais eles aparecem.

Selecione **[OK] e [conectar]** para se conectar ao servidor.

Uma vez conectado, várias experiências estão disponíveis:
- **Nova consulta**
- **Novo bloco de anotações**
- **Expanda a exibição do servidor e navegue/trabalhe nos objetos dentro do banco de dados**
- **...**

## <a name="next-step"></a>Próxima etapa
[Monitorar seu grupo de servidores](monitor-grafana-kibana.md)
