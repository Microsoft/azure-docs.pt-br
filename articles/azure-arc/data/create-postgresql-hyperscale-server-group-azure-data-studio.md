---
title: Criar hiperescala do PostgreSQL habilitado para Arc do Azure usando o Azure Data Studio
description: Criar hiperescala do PostgreSQL habilitado para Arc do Azure usando o Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e2007d8f0c558d35c0507b6e12bce6d6777fad52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92310911"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>Criar hiperescala do PostgreSQL habilitado para Arc do Azure usando o Azure Data Studio

Este documento orienta você pelas etapas para usar Azure Data Studio para provisionar grupos de servidores de hiperescala PostgreSQL habilitados para o Azure Arc.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connect-to-the-azure-arc-data-controller"></a>Conectar-se ao controlador de dados de arco do Azure

Antes de criar uma instância, faça logon no controlador de dados de arco do Azure se você ainda não estiver conectado.

```console
azdata login
```

Em seguida, você será solicitado a fornecer o namespace no qual o controlador de dados será criado, o nome de usuário e a senha para fazer logon no controlador.

> Se você precisar validar o namespace, poderá executar ```kubectl get pods -A``` para obter uma lista de todos os namespaces no cluster.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Etapa preliminar e temporária somente para usuários OpenShift

Implemente esta etapa antes de passar para a próxima etapa. Para implantar o grupo de servidores de hiperescala PostgreSQL no Red Hat OpenShift em um projeto diferente do padrão, você precisa executar os seguintes comandos em seu cluster para atualizar as restrições de segurança. Esse comando concede os privilégios necessários para as contas de serviço que executarão o grupo de servidores de hiperescala PostgreSQL. A SCC (restrição de contexto de segurança) **_Arc-data-SCC_** é aquela que você adicionou quando implantou o controlador de dados de arco do Azure.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**Server-Group-Name** é o nome do grupo de servidores que será implantado durante a próxima etapa._
   
Para obter mais detalhes sobre o SCCs no OpenShift, consulte a [documentação do OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Agora você pode implementar a próxima etapa.

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Criar um grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc

1. Iniciar Azure Data Studio
1. Na guia conexões, clique nos três pontos na parte superior esquerda e escolha "nova implantação"
1. Nas opções de implantação, selecione **grupo de servidores de hiperescala PostgreSQL-arco do Azure**
    >[!NOTE]
    > Você pode ser solicitado a instalar o [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] aqui se ele não estiver instalado no momento.
1. Aceite os termos de privacidade e licença e clique em **selecionar** na parte inferior
1. Na folha implantar grupo de servidores de hiperescala do PostgreSQL – arco do Azure, insira as seguintes informações:
   - Insira um nome para o grupo de servidores
   - Insira e confirme uma senha para o usuário administrador do _postgres_ do grupo de servidores
   - Selecione a classe de armazenamento conforme apropriado para os dados
   - Selecione a classe de armazenamento conforme apropriado para os logs
   - Selecione a classe de armazenamento conforme apropriado para backups
   - Selecione o número de nós de trabalho a serem provisionados
1. Clique no botão **implantar**

Isso inicia a criação do grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc no controlador de dados.

Em alguns minutos, sua criação deve ser concluída com êxito.

## <a name="next-steps"></a>Próximas etapas
- [Gerenciar seu grupo de servidores usando Azure Data Studio](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [Monitorar seu grupo de servidores](monitor-grafana-kibana.md)
- Leia os conceitos e os guias de instruções do banco de dados do Azure para PostgreSQL de hiperescala para distribuir seus dados em vários nós de hiperescala PostgreSQL e para se beneficiar de toda a potência do banco de postgres do Azure para o hiperescala. :
    * [Nós e tabelas](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinar o tipo de aplicativo](../../postgresql/concepts-hyperscale-app-type.md)
    * [Escolher uma coluna de distribuição](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocação de tabela](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuir e modificar tabelas](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Criar um banco de dados de vários locatários](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Criar um painel de análise em tempo real](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* Nos documentos acima, ignore as seções **entrar no portal do Azure**, & **criar um banco de dados do Azure para PostgreSQL-Citus (hiperescala)**. Implemente as etapas restantes em sua implantação do Azure Arc. Essas seções são específicas para o banco de dados do Azure para PostgreSQL Citus (hiperescala) oferecida como um serviço de PaaS na nuvem do Azure, mas as outras partes dos documentos são diretamente aplicáveis à sua hiperescala PostgreSQL habilitada para o Arc do Azure.

- [Escalar horizontalmente o grupo de servidores de Hiperescala do Banco de Dados do Azure para PostgreSQL](scale-out-postgresql-hyperscale-server-group.md)
- [Configuração de armazenamento e conceitos de armazenamento kubernetes](storage-configuration.md)
- [Modelo de recurso kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

