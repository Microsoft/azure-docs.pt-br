---
title: Criar uma restauração do aplicativo de cluster do Azure Red Hat OpenShift 4 usando o Velero
description: Saiba como criar uma restauração de seus aplicativos de cluster do Red Hat OpenShift do Azure usando o Velero
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc
ms.openlocfilehash: 8a9bb191390056caf7d5ba3b42c278205177d5a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102213004"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-restore"></a>Criar uma restauração do aplicativo de cluster do Azure Red Hat OpenShift 4

Neste artigo, você irá preparar seu ambiente para criar uma restauração do aplicativo de cluster do Azure Red Hat OpenShift 4. Você aprenderá a:

> [!div class="checklist"]
> * Configurar os pré-requisitos e instalar as ferramentas necessárias
> * Criar uma restauração de aplicativo do Azure Red Hat OpenShift 4

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá a execução da CLI do Azure versão 2.6.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Antes de começar

### <a name="create-an-azure-red-hat-openshift-4-application-backup"></a>Criar um backup de aplicativo do Azure Red Hat OpenShift 4

Para criar um backup de aplicativo do Red Hat OpenShift 4 do Azure, confira [criar um backup do Azure Red Hat OpenShift 4](./howto-create-a-backup.md)

## <a name="restore-an-azure-red-hat-openshift-4-application"></a>Restaurar um aplicativo do Azure Red Hat OpenShift 4

Essas etapas permitirão que você restaure um aplicativo cujo backup foi feito anteriormente com o Velero.
Você pode verificar a lista de backups que são atualmente reconhecidos pelo cluster para ver quais backups estão disponíveis para restauração.  Para fazer essa etapa, você precisará executar o seguinte comando:

_(Esta etapa pressupõe que você instalou Velero em um projeto chamado "Velero")_

```bash
oc get backups -n velero
```

Quando tiver o backup que deseja restaurar, você precisará executar a restauração com o seguinte comando:

```bash
velero restore create <name of restore> --from-backup <name of backup from above output list>
```

Esta etapa criará os objetos kubernetes dos quais foi feito backup da etapa anterior ao criar um backup.

Para ver o status da restauração, execute a seguinte etapa:

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
Quando a fase diz `Completed` , o aplicativo Red Hat 4 do Azure deve ser restaurado.

## <a name="restore-an-azure-red-hat-openshift-4-application-with-included-snapshots"></a>Restaurar um aplicativo Red Hat OpenShift 4 do Azure com instantâneos incluídos


Para criar uma restauração de um aplicativo Red Hat OpenShift 4 do Azure com volumes persistentes usando o Velero, você precisará executar a restauração com o seguinte comando:

```bash
velero restore create <name of the restore> --from-backup <name of backup from above output list> --exclude-resources="nodes,events,events.events.k8s.io,backups.ark.heptio.com,backups.velero.io,restores.ark.heptio.com,restores.velero.io"
```
Esta etapa criará os objetos kubernetes dos quais foi feito backup da etapa anterior ao criar um backup.

Para ver o status da restauração, execute a seguinte etapa:

```bash
oc get restore -n velero <name of restore created previously> -o yaml
```
Quando a fase diz `Completed` , o aplicativo Red Hat 4 do Azure deve ser restaurado.

Para obter mais informações sobre como criar backups e restaurações usando o Velero, consulte [backup OpenShift Resources da maneira nativa](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, um aplicativo de cluster do Azure Red Hat OpenShift 4 foi restaurado. Você aprendeu a:

> [!div class="checklist"]
> * Criar uma restauração de aplicativo de cluster OpenShift v4 usando Velero
> * Criar uma restauração de aplicativo de cluster OpenShift V4 com instantâneos usando Velero


Avance para o próximo artigo para saber mais sobre os recursos com suporte do Azure Red Hat OpenShift 4.

* [Recursos com suporte do Azure Red Hat OpenShift v4](supported-resources.md)