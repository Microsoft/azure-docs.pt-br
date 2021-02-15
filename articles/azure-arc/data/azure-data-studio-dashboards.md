---
title: Painéis de Azure Data Studio
description: Painéis de Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a96be6d4da3d292b2e9881652aad28f318ccee8a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107565"
---
# <a name="azure-data-studio-dashboards"></a>Painéis de Azure Data Studio

[Azure Data Studio](/sql/azure-data-studio/what-is) fornece uma experiência semelhante à portal do Azure para exibir informações sobre seus recursos de arco do Azure.  Essas exibições são chamadas de **painéis** e têm um layout e opções semelhantes ao que você poderia ver sobre um determinado recurso na portal do Azure, mas oferece a flexibilidade de ver essas informações localmente em seu ambiente em casos em que você não tem uma conexão disponível para o Azure.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connecting-to-a-data-controller"></a>Conectando a um controlador de dados

### <a name="prerequisites"></a>Pré-requisitos

- Baixar [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
- A extensão Arc do Azure está instalada

### <a name="determine-the-data-controller-server-api-endpoint-url"></a>Determinar a URL do ponto de extremidade da API do servidor do controlador de dados

Primeiro, você precisará se conectar Azure Data Studio à URL do ponto de extremidade da API de serviço do controlador de dados.

Para obter esse ponto de extremidade, você pode executar o seguinte comando:

```console
kubectl get svc/controller-svc-external -n <namespace name>

#Example:
kubectl get svc/controller-svc-external -n arc
```

Você verá uma saída parecida com esta:

```console
NAME                      TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                       AGE
controller-svc-external   LoadBalancer   10.0.175.137   52.154.152.24    30080:32192/TCP                               22h
```

Se você estiver usando um tipo de balanceador de carga, você desejará copiar o endereço IP externo e o número da porta. Se você estiver usando o NodePort, convém usar o endereço IP do servidor de API do kubernetes e o número da porta listado na coluna porta (S).

Agora, você desejará construir uma URL para seu ponto de extremidade combinando essas informações como:

```console
https://<ip address>:<port>

Example:
https://52.154.152.24:30080
```

Anote seu endereço IP, pois você o usará na próxima etapa.

### <a name="connect"></a>Conectar

1. Abrir o Azure Data Studio

1. Selecione a guia **conexões** à esquerda

Em direção à parte inferior, expanda o painel chamado **controladores de arco do Azure**.

Clique no ícone + para adicionar uma nova conexão de controlador de dados.

Na parte superior da tela na paleta de comandos, insira a URL que você construiu na etapa 1, clique em Enter.
Insira o nome de usuário para o controlador de dados.  Esse era o valor de nome de usuário que você passou durante a implantação do controlador de dados.  Clique em Enter.
Insira a senha para o controlador de dados.  Esse era o valor de senha que você passou durante a implantação do controlador de dados. Clique em Enter.

Agora que você está conectado a um controlador de dados, é possível exibir os painéis para o controlador de dados e quaisquer instâncias gerenciadas do SQL ou os recursos do grupo de servidores de hiperescala do PostgreSQL que você tem.

## <a name="view-the-data-controller-dashboard"></a>Exibir o painel do controlador de dados

Clique com o botão direito do mouse no controlador de dados no painel conexões no painel de **controladores de arco** expansível e escolha **gerenciar**.

Aqui você pode ver detalhes sobre o recurso do controlador de dados, como nome, região, modo de conexão, grupo de recursos, assinatura, ponto de extremidade do controlador e namespace.  Você também pode ver uma lista de todos os recursos gerenciados do banco de dados gerenciados pelo controlador.

Você observará que o layout é semelhante ao que você pode ver na portal do Azure.

Convenientemente, você pode iniciar a criação de uma instância gerenciada do SQL ou do grupo de servidores de hiperescala PostgreSQL clicando no botão + Nova instância.

Você também pode abrir o portal do Azure em contexto para esse controlador de dados clicando no botão abrir no portal do Azure.

## <a name="view-the-sql-managed-instance-dashboards"></a>Exibir os painéis da instância gerenciada do SQL

Se você tiver criado algumas instâncias gerenciadas do SQL, poderá vê-las listadas no painel conexões no painel expansível dos controladores de dados do Azure sob o controlador de dados que o está gerenciando.

Para exibir o painel de instância gerenciada do SQL para uma determinada instância, clique com o botão direito do mouse na instância e escolha gerenciar.

O painel de conexão será exibido à direita e solicitará o logon/senha para se conectar a essa instância do SQL. Se você souber as informações de conexão, poderá inseri-las e clicar em conectar.  Se você não sabe, você pode clicar em cancelar.  De qualquer forma, você será levado ao painel quando o painel de conexão for fechado.

Na guia Visão geral, você pode exibir detalhes sobre a instância gerenciada do SQL, como grupo de recursos, controlador de dados, ID da assinatura, status, região e muito mais.  Você também pode ver o link que pode clicar para entrar nos painéis Grafana ou Kibana no contexto para essa instância gerenciada do SQL.

Se você conseguir se conectar à instância do SQL Manage, poderá ver informações adicionais aqui.

Você pode excluir a instância gerenciada do SQL aqui ou abrir a portal do Azure para exibir a instância gerenciada do SQL no portal do Azure.

Se você clicar na guia cadeias de conexão à esquerda, poderá ver uma lista de cadeias de caracteres de conexão criadas previamente para essa instância gerenciada do SQL, facilitando a cópia/colagem em vários outros aplicativos ou código.

## <a name="view-the-postgresql-hyperscale-server-group-dashboards"></a>Exibir os painéis do grupo de servidores de hiperescala PostgreSQL

Se você tiver criado alguns grupos de servidores de hiperescala PostgreSQL, poderá vê-los listados no painel conexões no painel expansível dos controladores de dados do Azure sob o controlador de dados que o está gerenciando.

Para exibir o painel do grupo de servidores de hiperescala do PostgreSQL para um determinado grupo de servidores, clique com o botão direito do mouse no grupo de servidores e escolha gerenciar.

Na guia Visão geral, você pode exibir detalhes sobre o grupo de servidores, como grupo de recursos, controlador de dados, ID da assinatura, status, região e muito mais.  Você também pode ver o link que pode clicar para entrar nos painéis Grafana ou Kibana no contexto para esse grupo de servidores.

Você pode excluir o grupo de servidores aqui ou abrir o portal do Azure para exibir o grupo de servidores na portal do Azure.

Se você clicar na guia cadeias de conexão à esquerda, poderá ver uma lista de cadeias de conexão pré-projetadas para esse grupo de servidores, facilitando a cópia/colagem em vários outros aplicativos ou código.

Se você clicar na guia Propriedades à esquerda, poderá ver detalhes adicionais.

Se você clicar na guia Resource Health à esquerda, poderá ver a integridade de alto nível atual desse grupo de servidores.

Se você clicar na guia diagnosticar e resolver problemas à esquerda, poderá iniciar o notebook de solução de problemas do PostgreSQL.

Se você clicar na guia nova solicitação de suporte à esquerda, poderá iniciar o portal do Azure em contexto para o grupo de servidores e criar uma solicitação de suporte do Azure a partir daí.