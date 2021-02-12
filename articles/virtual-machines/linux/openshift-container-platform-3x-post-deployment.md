---
title: Plataforma de contêiner OpenShift 3,11 em tarefas de pós-implantação do Azure
description: Tarefas adicionais para depois que um cluster da plataforma de contêiner OpenShift 3,11 foi implantado.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible, devx-track-azurecli
ms.openlocfilehash: dd967ad08b628f9073edfe548033f7e97845d047
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016056"
---
# <a name="post-deployment-tasks"></a>Tarefas de pós-implantação

Depois de implantar um cluster OpenShift, você pode configurar itens adicionais. Este artigo cobre:

- Como configurar o logon único entre o Active Directory do Azure (Azure AD)
- Como configurar logs de Azure Monitor para monitorar o OpenShift
- Como configurar métricas e logs
- Como instalar o Open Service Broker para o Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Configurar o Logon Único usando o Azure Active Directory

Para usar o Azure Active Directory para autenticação, primeiro você precisa criar um registro de aplicativo do Azure AD. Esse processo envolve duas etapas: o registro do aplicativo de criação e configuração de permissões.

### <a name="create-an-app-registration"></a>Criar o Registro do aplicativo

Essas etapas usam a CLI do Azure para criar o Registro do aplicativo e a GUI (Portal) para definir as permissões. Para criar o Registro do aplicativo, são necessárias cinco informações:

- Nome de exibição: nome de registro do aplicativo (ex: OCPAzureAD)
- Home Page: URL do console do OpenShift (por exemplo, `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- URI do identificador: URL do console do OpenShift (por exemplo, `https://masterdns343khhde.westus.cloudapp.azure.com/console` )
- URL de resposta: URL pública mestre e o nome de registro do aplicativo (por exemplo, `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD` )
- Senha: senha de segurança (use uma senha forte)

O exemplo a seguir criará um Registro de aplicativo usando as informações acima:

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

Se o comando for bem-sucedido, você obterá uma saída JSON semelhante a esta:

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

Anote a propriedade appId retornada do comando para uma etapa posterior.

No Portal do Azure:

1. Selecione **Azure Active Directory**  >  **registro de aplicativo**.
2. Pesquise o Registro do seu aplicativo (ex: OCPAzureAD).
3. Nos resultados, clique no Registro do aplicativo.
4. Em **Configurações**, selecione **Permissões necessárias**.
5. Em **Permissões necessárias**, selecione **Adicionar**.

   ![Registro do Aplicativo](media/openshift-post-deployment/app-registration.png)

6. Clique em Etapa 1: selecionar a API e, em seguida, clique em **Microsoft Azure Active Directory (Microsoft.Azure.ActiveDirectory)**. Na parte inferior, selecione **Selecionar**.

   ![Registro de aplicativo Selecionar API](media/openshift-post-deployment/app-registration-select-api.png)

7. Na Etapa 2: selecionar as permissões, selecione **Entrar e ler o perfil do usuário** em **Permissões delegadas** e clique em **Selecionar**.

   ![Registro de aplicativo Acesso](media/openshift-post-deployment/app-registration-access.png)

8. Selecione **Concluído**.

### <a name="configure-openshift-for-azure-ad-authentication"></a>Configurar o OpenShift para autenticação do Azure AD

Para configurar o OpenShift para usar o Azure AD como um provedor de autenticação, o arquivo /etc/origin/master/master-config.yaml precisa ser editado em todos os nós mestres.

A Id do locatário pode ser encontrada usando o seguinte comando da CLI:

```azurecli
az account show
```

No arquivo yaml, localize as seguintes linhas:

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

Insira as seguintes linhas imediatamente após as linhas acima:

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

Certifique-se de que o texto esteja alinhado corretamente em identityProviders. A Id do locatário pode ser encontrada usando o seguinte comando da CLI: ```az account show```

Reinicie os serviços do OpenShift Master em todos os nós mestres:

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

No Console do OpenShift, agora você verá duas opções para autenticação – htpasswd_auth e [Registro de Aplicativo].

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Monitorar OpenShift com logs de Azure Monitor

Existem três maneiras de adicionar o agente do Log Analytics ao OpenShift.
- Instale o agente do Log Analytics para Linux diretamente em cada nó do OpenShift
- Habilitar Azure Monitor extensão de VM em cada nó do OpenShift
- Instalar o agente de Log Analytics como um OpenShift daemon-Set

Leia as [instruções](../../azure-monitor/insights/containers.md#configure-a-log-analytics-agent-for-red-hat-openshift) completas para obter mais detalhes.

## <a name="configure-metrics-and-logging"></a>Configurar métricas e logs

Com base na ramificação, os modelos do Azure Resource Manager para OpenShift Container Platform e OKD podem fornecer parâmetros de entrada para permitir métricas e registro como parte da instalação.

A oferta do OpenShift Container Platform Marketplace também oferece uma opção para ativar as métricas e o registro em log durante a instalação do cluster.

Se as métricas / registros não foram ativados durante a instalação do cluster, eles podem ser facilmente ativados após o fato.

### <a name="azure-cloud-provider-in-use"></a>Azure Cloud Provider em uso

SSH para o bastião ou primeiro nó mestre (com base no modelo e na ramificação em uso) usando as credenciais fornecidas durante a implementação. Emita o seguinte comando:

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>Azure Cloud Provider não usado

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Instalar o Service Broker para o Azure (OSBA)

O Open Service Broker para Azure, ou OSBA, permite provisionar os Serviços de Nuvem do Azure diretamente do OpenShift. OSBA em uma implementação da API do Open Service Broker para o Azure. A API do Open Service Broker é uma especificação que define um idioma comum para provedores de nuvem que os aplicativos nativos da nuvem podem usar para gerenciar serviços de nuvem sem bloqueio.

Para instalar o OSBA no OpenShift, siga as instruções localizadas aqui: https://github.com/Azure/open-service-broker-azure#openshift-project-template. 
> [!NOTE]
> Conclua apenas as etapas na seção modelo de projeto OpenShift e não na seção instalação inteira.

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao OpenShift Container Platform](https://docs.openshift.com)
