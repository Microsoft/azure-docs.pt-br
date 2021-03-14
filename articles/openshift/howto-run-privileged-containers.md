---
title: Executar contêineres privilegiados em um cluster do Azure Red Hat OpenShift | Microsoft Docs
description: Execute contêineres privilegiados para monitorar a segurança e a conformidade.
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 12/05/2019
keywords: toa, openshift, aquasec, Twistlock, Red Hat
ms.openlocfilehash: 77e75232b7f9d23f1d07931cc7dc231174e1312d
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633555"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Executar contêineres privilegiados em um cluster do Red Hat OpenShift no Azure

> [!IMPORTANT]
> O Azure Red Hat OpenShift 3,11 será desativado em 30 de junho de 2022. O suporte para a criação de novos clusters do Azure Red Hat OpenShift 3,11 continua até 30 de novembro de 2020. Após a aposentadoria, os clusters restantes do Azure Red Hat OpenShift 3,11 serão desligados para evitar vulnerabilidades de segurança.
> 
> Siga este guia para [criar um cluster do Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Se você tiver dúvidas específicas, [entre em contato conosco](mailto:arofeedback@microsoft.com).

Não é possível executar contêineres com privilégios arbitrários em clusters do Azure Red Hat OpenShift.
Duas soluções de monitoramento e conformidade de segurança podem ser executadas em clusters de toa.
Este documento descreve as diferenças da documentação de implantação genérica do OpenShift dos fornecedores de produtos de segurança.


Leia estas instruções antes de seguir as instruções do fornecedor.
Os títulos de seção nas etapas específicas do produto abaixo referem-se diretamente aos títulos das seções na documentação dos fornecedores.

## <a name="before-you-begin"></a>Antes de começar

A documentação da maioria dos produtos de segurança pressupõe que você tenha privilégios de administrador de cluster.
Os administradores do cliente não têm todos os privilégios no Azure Red Hat OpenShift. As permissões necessárias para modificar os recursos de todo o cluster são limitadas.

Primeiro, verifique se o usuário está conectado ao cluster como administrador do cliente, executando `oc get scc` . Todos os usuários que são membros do grupo administrador do cliente têm permissões para exibir as restrições de contexto de segurança (SCCs) no cluster.

Em seguida, verifique se a `oc` versão binária é `3.11.154` .
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Etapas específicas do produto para segurança de água
As instruções básicas que serão modificadas podem ser encontradas na [documentação de implantação de segurança em água](https://docs.aquasec.com/docs/openshift-red-hat). As etapas aqui serão executadas em conjunto com a documentação de implantação em água.

A primeira etapa é anotar o SCCs necessário que será atualizado. Essas anotações impedem que o pod de sincronização do cluster reverte todas as alterações feitas nesses SSCs.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Etapa 1: preparar os pré-requisitos
Lembre-se de fazer logon no cluster como um administrador de cliente de toa em vez da função de administrador de cluster.

Crie o projeto e a conta de serviço.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Em vez de atribuir a função de leitor de cluster, atribua a função Customer-admin-cluster à conta azul com o comando a seguir.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Continue seguindo as instruções restantes na etapa 1.  Essas instruções descrevem a configuração do segredo para o registro em azul-piscina.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Etapa 2: implantar o servidor, o banco de dados e o gateway de água
Siga as etapas fornecidas na documentação da água para instalar a água-console. YAML.

Modifique o fornecido `aqua-console.yaml` .  Remova os dois primeiros objetos rotulados `kind: ClusterRole` e `kind: ClusterRoleBinding` .  Esses recursos não serão criados, pois o administrador do cliente não tem permissão no momento para modificar `ClusterRole` e `ClusterRoleBinding` objetos.

A segunda modificação será feita na `kind: Route` parte do `aqua-console.yaml` . Substitua o seguinte YAML para o `kind: Route` objeto no `aqua-console.yaml` arquivo.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Siga as instruções restantes.

### <a name="step-3-login-to-the-aqua-server"></a>Etapa 3: fazer logon no servidor de azul-piscina
Esta seção não é modificada de nenhuma forma.  Siga a documentação da água.

Use o comando a seguir para obter o endereço do console em água.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Etapa 4: implantar os executores de água
Defina os seguintes campos ao implantar os imforçadores:

| Campo          | Valor         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiceAccount | água-conta  |
| Project        | água-segurança |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Etapas específicas do produto para prisma Cloud/Twistlock

As instruções básicas que vamos modificar podem ser encontradas na [documentação de implantação do prisma Cloud](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Comece instalando a `twistcli` ferramenta conforme descrito nas seções "instalar a nuvem do prisma" e "baixar o software de nuvem prisma".

Criar um novo projeto OpenShift
```
oc new-project twistlock
```

Ignore a seção opcional "enviar por push as imagens de nuvem prisma para um registro privado". Ele não funcionará no Azure Red Hat OpenShift. Em vez disso, use o registro online.

Você pode seguir a documentação oficial ao aplicar as correções descritas abaixo.
Comece com a seção "instalar console".

### <a name="install-console"></a>Instalar console

Durante `oc create -f twistlock_console.yaml` a etapa 2, você receberá um erro ao criar o namespace.
Você pode ignorá-lo com segurança, o namespace foi criado anteriormente com o `oc new-project` comando.

Use `azure-disk` para o tipo de armazenamento.

### <a name="create-an-external-route-to-console"></a>Criar uma rota externa para o console

Você pode seguir a documentação ou as instruções abaixo se preferir o comando OC.
Copie a seguinte definição de rota para um arquivo chamado twistlock_route. YAML em seu computador
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
em seguida, execute:
```
oc create -f twistlock_route.yaml
```

Você pode obter a URL atribuída ao console do Twistlock com este comando: `oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Configurar console

Siga a documentação do Twistlock.

### <a name="install-defender"></a>Instalar o defender

Durante `oc create -f defender.yaml` a etapa 2, você receberá erros ao criar a função de cluster e a associação de função de cluster.
Você pode ignorá-las.

Os defensores serão implantados somente em nós de computação. Você não precisa limitá-los com um seletor de nó.
