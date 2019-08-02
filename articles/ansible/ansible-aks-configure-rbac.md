---
title: Tutorial – Configurar funções de controle de acesso baseado em função (RBAC) no Serviço de Kubernetes do Azure (AKS) usando o Ansible | Microsoft Docs
description: Saiba como usar o Ansible para configurar o RBAC no cluster do Serviço de Kubernetes do Azure (AKS)
keywords: ansible, azure, devops, bash, cloudshell, guia estratégico, aks, contêiner, aks, kubernetes, azure active directory, rbac
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: eae23806ee1b4e2dac1d3410e32c3242e89d4be8
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719826"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>Tutorial: Configurar funções de controle de acesso baseado em função (RBAC) no Serviço de Kubernetes do Azure (AKS) usando o Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

O AKS pode ser configurado para usar o [Azure AD (Active Directory)](/azure/active-directory/) na autenticação do usuário. Uma vez configurado, você usa seu token de autenticação do Azure AD para entrar no cluster do AKS. O RBAC pode ser baseado na identidade de um usuário ou na associação do grupo de diretórios.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Criar um cluster AKS habilitado para Azure AD
> * Configurar uma função RBAC no cluster

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **Instalar a biblioteca do RedHat OpenShift** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>Configurar o Azure AD para autenticação do AKS

Ao configurar o Azure AD para autenticação do AKS, dois aplicativos do Azure AD são configurados. Essa operação deve ser concluída por um administrador de locatário do Azure. Para obter mais informações, confira [Integrar o Azure Active Directory ao AKS](/azure/aks/aad-integration#create-the-server-application). 

Do administrador do locatário do Azure, obtenha os seguintes valores:

- Segredo do aplicativo do servidor
- ID do aplicativo do servidor
- ID do aplicativo cliente 
- ID do locatário

Esses valores são necessários para executar o guia estratégico de exemplo.  

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

Nesta seção, você criará um AKS com o [aplicativo do Azure AD](#configure-azure-ad-for-aks-authentication).

Há algumas observações importantes a serem consideradas ao trabalhar com o guia estratégico de exemplo:

- O guia estratégico carrega `ssh_key` a partir de `~/.ssh/id_rsa.pub`. Se for modificá-lo, use o formato de linha única, começando com "ssh-rsa" (sem as aspas).
- Os valores `client_id` e `client_secret` são carregados de `~/.azure/credentials`, que é o arquivo de credencial padrão. Você pode definir esses valores para sua entidade de serviço ou carregar esses valores a partir de variáveis de ambiente:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

Salve o guia estratégico a seguir como `aks-create.yml`:

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aksversion_facts:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>Obter a ID de Objeto do Azure AD

Para criar uma associação de RBAC, primeiro você precisa obter a ID de Objeto do Azure AD. 

1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. No campo de pesquisa, na parte superior da página, insira `Azure Active Directory`. 

1. Clique em `Enter`.

1. No menu **Gerenciar**, selecione **Usuários**.

1. No campo do nome, pesquise sua conta.

1. Na coluna **Nome**, selecione o link para sua conta.

1. Na seção **Identidade**, copie a **ID do Objeto**.

    ![Copie a ID de Objeto do Azure AD.](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>Criar associação do RBAC

Nesta seção, você cria uma associação de função ou uma associação de função de cluster no AKS. 

Salve o guia estratégico a seguir como `kube-role.yml`:

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

Substitua o espaço reservado `&lt;your-aad-account>` por sua [ID de Objeto](#get-the-azure-ad-object-id) de locatário do Azure AD.

Salve o guia estratégico a seguir, que implanta sua nova função no AKS, como `aks-kube-deploy.yml`:

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>Executar o guia estratégico de exemplo

Esta seção lista o guia estratégico de exemplo completo que chama as tarefas criadas neste artigo. 

Salve o guia estratégico a seguir como `aks-rbac.yml`:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

Na seção `vars`, substitua os seguintes espaços reservados por suas informações do Azure AD:

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

Execute o guia estratégico completo usando o comando `ansible-playbook`:

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>Verifique os resultados

Nesta seção, você usa o kubectl para listar os nós criados neste artigo.

Insira o comando a seguir em um prompt do terminal:

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

O comando irá direcionar você para uma página de autenticação. Faça logon usando sua conta do Azure.

Uma vez autenticado, o kubectl lista os nós de maneira similar aos seguintes resultados:

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessários, exclua os recursos criados neste artigo. 

Salve o seguinte código como `cleanup.yml`:

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

Execute o guia estratégico usando o comando `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Ansible no Azure](/azure/ansible/)
