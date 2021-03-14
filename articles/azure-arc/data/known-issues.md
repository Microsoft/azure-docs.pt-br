---
title: Serviços de dados habilitados para Arc do Azure-problemas conhecidos
description: Problemas mais recentes conhecidos
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: 8100d9e12f107e0c4598876c46453b46c6ee4d0e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121993"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>Problemas conhecidos-serviços de dados habilitados para o Azure Arc (versão prévia)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>Fevereiro de 2021

- O modo de cluster conectado está desabilitado

## <a name="introduced-prior-to-february-2021"></a>Introduzido antes de fevereiro de 2021

### <a name="data-controller"></a>Controlador de dados

- No serviço de kubernetes do Azure (AKS), não há suporte para kubernetes versão 1.19. x.
- No kubernetes 1,19 `containerd` não tem suporte.
- O recurso de controlador de dados no Azure é atualmente um recurso do Azure. Todas as atualizações como excluir não são propagadas de volta para o cluster kubernetes.
- Os nomes de instância não podem ter mais de 13 caracteres
- Nenhuma atualização in-loco para o controlador de dados do Arc do Azure ou instâncias de banco de dado.
- As imagens de contêiner dos serviços de dados habilitados para Arc não foram assinadas.  Talvez seja necessário configurar os nós do Kubernetes para permitir que as imagens de contêiner não assinadas sejam recebidas.  Por exemplo, se você estiver usando o Docker como o tempo de execução do contêiner, poderá definir a variável de ambiente DOCKER_CONTENT_TRUST = 0 e reiniciar.  Outros runtimes de contêiner têm opções semelhantes, como em [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Não é possível criar instâncias gerenciadas SQL do ARC ou grupos de servidores de hiperescala PostgreSQL habilitados do Azure no portal do Azure.
- Por enquanto, se você estiver usando o NFS, precisará definir `allowRunAsRoot` como `true` em seu arquivo de perfil de implantação antes de criar o controlador de dados de arco do Azure.
- Somente autenticação de logon do SQL e PostgreSQL.  Não há suporte para Azure Active Directory ou Active Directory.
- A criação de um controlador de dados no OpenShift requer restrições de segurança relaxadas.  Confira a documentação para obter detalhes.
- Se você estiver usando o mecanismo do AKS (serviço de kubernetes do Azure) no Hub Azure Stack com o controlador de dados Arc do Azure e instâncias de banco de dado, não haverá suporte para a atualização para uma versão mais recente do kubernetes. Desinstale o controlador de dados Arc do Azure e todas as instâncias de banco de dado antes de atualizar o cluster kubernetes.
- Os clusters AKS que abrangem [várias zonas de disponibilidade](../../aks/availability-zones.md) não têm suporte no momento para serviços de dados habilitados para Arc do Azure. Para evitar esse problema, ao criar o cluster AKS no portal do Azure, se você selecionar uma região em que as zonas estão disponíveis, desmarque todas as zonas do controle de seleção. Veja a seguinte imagem:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Desmarque as caixas de seleção de cada zona para especificar nenhuma.":::

### <a name="postgresql"></a>PostgreSQL

- A hiperescala do PostgreSQL habilitado para Arc do Azure retorna uma mensagem de erro imprecisa quando não pode restaurar para o ponto relativo no tempo que você indica. Por exemplo, se você especificou um ponto no tempo para restaurar que é mais antigo do que os seus backups contêm, a restauração falhará com uma mensagem de erro como: `ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}`
Quando isso acontecer, reinicie o comando depois de indicar um ponto no tempo que está dentro do intervalo de datas para o qual você tem backups. Você determinará esse intervalo listando os backups e examinando as datas em que eles foram tirados.
- Há suporte para a restauração pontual somente em grupos de servidores. O servidor de destino de uma operação de restauração pontual não pode ser o servidor do qual você realizou o backup. Ele deve ser um grupo de servidores diferente. No entanto, a restauração completa tem suporte no mesmo grupo de servidores.
- Uma ID de backup é necessária ao fazer uma restauração completa. Por padrão, se você não estiver indicando uma ID de backup, o backup mais recente será usado. Isso não funciona nesta versão.

## <a name="next-steps"></a>Próximas etapas

> **Quer apenas experimentar as novidades?**  
> Comece rapidamente com o [Azure Arc JumpStart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) no AKs, AWS elástico kubernetes Service (EKS), Google Cloud kubernetes Engine (GKE) ou em uma VM do Azure.

- [Instalar as ferramentas de cliente](install-client-tools.md)
- [Criar o controlador de dados do Azure Arc](create-data-controller.md) (é necessária a instalação das ferramentas de cliente primeiro)
- [Criar uma instância gerenciada de SQL do Azure no Azure Arc](create-sql-managed-instance.md) (é necessária a criação de um controlador de dados do Azure Arc primeiro)
- [Criar um grupo de servidores de Hiperescala do Banco de Dados do Azure para PostgreSQL no Azure Arc](create-postgresql-hyperscale-server-group.md) (é necessária a criação de um controlador de dados do Azure Arc primeiro)
- [Provedores de recursos para serviços do Azure](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [Notas de versão – serviços de dados habilitados para o Azure Arc (versão prévia)](release-notes.md)
