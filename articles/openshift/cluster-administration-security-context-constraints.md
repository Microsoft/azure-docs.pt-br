---
title: Gerenciar restrições de contexto de segurança no Red Hat OpenShift no Azure | Microsoft Docs
description: Restrições de contexto de segurança para administradores de cluster do Red Hat OpenShift no Azure
services: container-service
author: troy0820
ms.author: b-trconn
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 977504c1faec9bd8134646a8cbe31f9eea665edd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100636196"
---
# <a name="manage-security-context-constraints-in-azure-red-hat-openshift"></a>Gerencia restrições de contexto de segurança no Red Hat OpenShift no Azure

> [!IMPORTANT]
> O Red Hat OpenShift 3.11 no Azure será desativado em 30 de junho de 2022. O suporte para a criação de clusters do Red Hat OpenShift 3.11 no Azure continuará até 30 de novembro de 2020. Após a desativação, os clusters restantes do Red Hat OpenShift 3.11 no Azure serão desligados para evitar vulnerabilidades de segurança.
> 
> Siga este guia para [criar um cluster do Red Hat OpenShift 4 no Azure](tutorial-create-cluster.md).
> Se você tiver perguntas específicas, [entre em contato conosco](mailto:arofeedback@microsoft.com).

As SCCs (restrições de contexto de segurança) permitem que os administradores de cluster controlem as permissões dos pods. Para saber mais sobre esse tipo de API, confira a [documentação da arquitetura para as SCCs](https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html). Você pode gerenciar as SCCs na sua instância como objetos normais de API usando a CLI.

## <a name="list-security-context-constraints"></a>Listar as restrições de contexto de segurança

Para obter uma lista atual das SCCs, use este comando: 

```bash
$ oc get scc

NAME               PRIV      CAPS      SELINUX     RUNASUSER          FSGROUP     SUPGROUP    PRIORITY   READONLYROOTFS   VOLUMES
anyuid             false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    10         false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
hostaccess         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath persistentVolumeClaim secret]
hostmount-anyuid   false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath nfs persistentVolumeClaim secret]
hostnetwork        false     []        MustRunAs   MustRunAsRange     MustRunAs   MustRunAs   <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
nonroot            false     []        MustRunAs   MustRunAsNonRoot   RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
privileged         true      [*]       RunAsAny    RunAsAny           RunAsAny    RunAsAny    <none>     false            [*]
restricted         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
```

## <a name="examine-an-object-for-security-context-constraints"></a>Examinar um objeto em busca de restrições de contexto de segurança

Para examinar uma SCC específica, use `oc get`, `oc describe` ou `oc edit`.  Por exemplo, para examinar a SCC **restrita**, use este comando:
```bash
$ oc describe scc restricted
Name:                    restricted
Priority:                <none>
Access:
  Users:                <none>
  Groups:                system:authenticated
Settings:
  Allow Privileged:            false
  Default Add Capabilities:        <none>
  Required Drop Capabilities:        KILL,MKNOD,SYS_CHROOT,SETUID,SETGID
  Allowed Capabilities:            <none>
  Allowed Seccomp Profiles:        <none>
  Allowed Volume Types:            configMap,downwardAPI,emptyDir,persistentVolumeClaim,projected,secret
  Allow Host Network:            false
  Allow Host Ports:            false
  Allow Host PID:            false
  Allow Host IPC:            false
  Read Only Root Filesystem:        false
  Run As User Strategy: MustRunAsRange
    UID:                <none>
    UID Range Min:            <none>
    UID Range Max:            <none>
  SELinux Context Strategy: MustRunAs
    User:                <none>
    Role:                <none>
    Type:                <none>
    Level:                <none>
  FSGroup Strategy: MustRunAs
    Ranges:                <none>
  Supplemental Groups Strategy: RunAsAny
    Ranges:                <none>
```
## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Criar um cluster do Red Hat OpenShift no Azure](tutorial-create-cluster.md) 
