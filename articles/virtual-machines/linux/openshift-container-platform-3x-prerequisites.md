---
title: Plataforma de contêiner OpenShift 3,11 em pré-requisitos do Azure
description: Pré-requisitos para implantar a plataforma de contêiner OpenShift 3,11 no Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 645402d6c6b1fe744938ef5597098f46bc80c78e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672365"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Pré-requisitos comuns para implantar a plataforma de contêiner do OpenShift 3,11 no Azure

Este artigo descreve pré-requisitos comuns para implantar o OpenShift Container Platform ou OKD no Azure.

A instalação do OpenShift é feita por meio de guias estratégicos do Ansible. Ansible usa Secure Shell (SSH) para se conectar a todos os hosts de cluster para concluir as etapas de instalação.

Quando o Ansible faz a conexão SSH com os hosts remotos, ele não pode inserir uma senha. Por esse motivo, a chave privada não pode ter uma senha (senha) associada a ela ou a implantação falhará.

Como todas as máquinas virtuais (VMs) são implantadas por meio de modelos do Azure Resource Manager, a mesma Chave pública é usada para acessar todas as VMs. A chave privada correspondente deve estar na VM que executa todos os guias estratégicos também. Para executar essa ação com segurança, um cofre de chaves do Azure é usado para passar a chave privada para a VM.

Se houver uma necessidade de armazenamento persistente para contêineres, então Volumes Persistentes serão necessários. O OpenShift dá suporte a VHDs (discos rígidos virtuais) do Azure para volumes persistentes, mas o Azure deve primeiro ser configurado como o provedor de nuvem.

Nesse modelo, o OpenShift:

- Cria um objeto VHD em uma conta de armazenamento do Azure ou em um disco gerenciado.
- Monta o VHD em uma VM e formata o volume.
- Montará o volume no Pod.

Para que isso funcione, o OpenShift precisa de permissões para executar as tarefas no Azure. Uma entidade de serviço é usada para essa finalidade. A Entidade de serviço é uma conta de segurança no Azure Active Directory que tem as permissões para recursos.

A Entidade de serviço deve ter acesso a Contas de armazenamento e máquinas virtuais que compõem o cluster. Se todos os recursos de cluster do OpenShift forem implantados em um único Grupo de recursos, a SP pode receber permissões para esse Grupo de recursos.

Este guia descreve como criar os artefatos associados aos pré-requisitos.

> [!div class="checklist"]
> * Crie um KeyVault para gerenciar chaves SSH para o cluster OpenShift.
> * Crie uma Entidade de serviço para uso pelo Provedor de nuvem do Azure.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure 
Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/reference-index) e siga as instruções na tela ou clique em **Experimentar** para usar o Cloud Shell.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Você deve usar um grupo de recursos dedicado para hospedar o cofre de chaves. Esse grupo é separado do grupo de recursos no qual os recursos de cluster OpenShift implantam.

O exemplo abaixo cria um grupo de recursos denominado *keyvaultrg* no local *eastus*:

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Criar um cofre de chaves
Crie um KeyVault para armazenar as chaves de SSH para o cluster com o comando [az keyvault create](/cli/azure/keyvault). O nome do cofre de chaves deve ser globalmente exclusivo e deve ser habilitado para implantação de modelo, ou a implantação falhará com o erro "KeyVaultParameterReferenceSecretRetrieveFailed".

O exemplo abaixo cria um keyvault denominado *keyvault* no grupo de recursos *keyvaultrg*:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Criar uma chave SSH 
Uma chave SSH é necessária para proteger o acesso ao cluster de OpenShift. Crie um par de chaves SSH usando o comando `ssh-keygen` (no Linux ou Mac):
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> O par de chaves SSH não pode ter uma senha / frase secreta.

Para obter mais informações sobre as chaves SSH no Windows, confira [Como criar chaves SSH no Windows](./ssh-from-windows.md). Certifique-se de exportar a chave privada no formato OpenSSH.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Armazenar chave privada SSH no Azure Key Vault
A implantação de OpenShift usa a chave SSH criada para proteger o acesso ao mestre OpenShift. Para habilitar a implantação para recuperar a chave SSH com segurança, armazene a chave no Key Vault usando o comando a seguir:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço 
O OpenShift se comunica com o Azure usando um nome de usuário e a senha ou uma entidade de serviço. Uma entidade de serviço do Azure é uma identidade de segurança que você pode usar com aplicativos, serviços e ferramentas de automação como o OpenShift. Você controla e define as permissões referentes a quais operações a entidade de serviço pode executar no Azure. É melhor fazer o escopo das permissões da entidade de serviço para grupos de recursos específicos em vez de toda a assinatura.

Crie uma entidade de serviço com [az ad sp create-for-rbac](/cli/azure/ad/sp) e gere as credenciais necessárias para o OpenShift.

O exemplo a seguir cria uma entidade de serviço e atribui permissões de colaborador de ti a um grupo de recursos chamado *openshiftrg*.

Primeiro, crie o grupo de recursos chamado *openshiftrg*:

```azurecli
az group create -l eastus -n openshiftrg
```

Criar uma entidade de serviço:

```azurecli
az group show --name openshiftrg --query id
```

Salvar a saída do comando e usar no lugar de $scope no próximo comando

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Anote a propriedade appId e a senha retornada do comando:

```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```

 > [!WARNING] 
 > Certifique-se de anotar a senha segura, pois não será possível recuperar essa senha novamente.

Para obter mais informações sobre entidades de serviço, confira [Criar entidade de serviço do Azure com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Pré-requisitos aplicáveis somente ao modelo do Resource Manager

Os segredos precisarão ser criados para a chave privada SSH (**sshPrivateKey**), o segredo do cliente do Azure AD (**aadClientSecret**), a senha de administrador do OpenShift (**openshiftPassword**) e a senha ou a chave de ativação do Red Hat Subscription Manager (**rhsmPasswordOrActivationKey**).  Além disso, se forem usados certificados TLS/SSL personalizados, seis segredos adicionais precisarão ser criados- **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile** e **masterkeyfile**.  Esses parâmetros serão explicados em mais detalhes.

O modelo referencia nomes de segredo específicos, portanto, você **deve** usar os nomes em negrito listados acima (diferencia maiúsculas de minúsculas).

### <a name="custom-certificates"></a>Certificados personalizados

Por padrão, o modelo implantará um cluster OpenShift usando certificados autoassinados para o console Web do OpenShift e o domínio de roteamento. Se você quiser usar certificados TLS/SSL personalizados, defina ' routingCertType ' como ' Custom ' e ' masterCertType ' como ' Custom '.  Você precisará da autoridade de certificação, do certificado e dos arquivos de chave no formato. pem para os certificados.  É possível usar certificados personalizados para um, mas não para o outro.

Você precisará armazenar esses arquivos em Key Vault segredos.  Use o mesmo Key Vault como aquele usado para a chave privada.  Em vez de exigir 6 entradas adicionais para os nomes de segredo, o modelo é embutido em código para usar nomes de segredo específicos para cada um dos arquivos de certificado TLS/SSL.  Armazene os dados do certificado usando as informações da tabela a seguir.

| Nome do segredo      | Arquivo de certificado   |
|------------------|--------------------|
| mastercafile     | arquivo de AC mestre     |
| mastercertfile   | arquivo de certificado mestre   |
| masterkeyfile    | arquivo de chave mestra    |
| routingcafile    | arquivo de AC de roteamento    |
| routingcertfile  | arquivo de certificado de roteamento  |
| routingkeyfile   | arquivo de chave de roteamento   |

Crie os segredos usando o CLI do Azure. Veja abaixo um exemplo.

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Próximas etapas

Este artigo abordou os seguintes tópicos:
> [!div class="checklist"]
> * Crie um KeyVault para gerenciar chaves SSH para o cluster OpenShift.
> * Crie uma Entidade de serviço para uso pelo Provedor de Solução de nuvem do Azure.

Agora, implante um cluster de OpenShift:

- [Implantar a plataforma de contêiner do OpenShift](./openshift-container-platform-3x.md)
- [Implantar a plataforma de contêiner OpenShift Self-Managed oferta do Marketplace](./openshift-container-platform-3x-marketplace-self-managed.md)
