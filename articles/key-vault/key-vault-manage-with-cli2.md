---
title: Gerenciar o Azure Key Vault usando a CLI - Azure Key Vault | Microsoft Docs
description: Use este artigo para automatizar tarefas comuns no Key Vault usando a CLI do Azure
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: barclayn
ms.openlocfilehash: 1679fbe0dedc88ca3e8293512f9a79bb7da69790
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60641017"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Gerenciar o Key Vault usando a CLI do Azure 

Este artigo aborda como começar a trabalhar com o Azure Key Vault usando a CLI do Azure.  Você pode ver informações sobre:

- Como criar um contêiner protegido (um cofre) no Azure
- Adicionar uma chave, segredo ou certificado ao cofre de chaves
- Registrar um aplicativo com o Azure Active Directory
- Autorizar um aplicativo a usar uma chave ou segredo
- Definir as políticas de acesso avançado do cofre de chaves
- Trabalhar com HSMs (módulos de segurança de hardware)
- Excluir o cofre de chave e chaves e segredos associados
- Outros comandos da interface de linha de comando de plataforma cruzada do Azure


O Cofre da Chave do Azure está disponível na maioria das regiões. Para obter mais informações, consulte a [Página de preços do Cofre da Chave](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Este artigo não inclui instruções sobre como gravar o aplicativo do Azure incluído em uma das etapas, que mostra como autorizar um aplicativo a usar uma chave ou um segredo no cofre de chaves.
>

Para obter uma visão geral do Azure Key Vault, consulte [O que é o Azure Key Vault?](key-vault-whatis.md)
Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para usar comandos da CLI do Azure neste artigo, você deve ter os seguintes itens:

* Uma assinatura do Microsoft Azure. Se você não tiver uma, pode se inscrever e fazer uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* Interface de Linha de Comando do Azure versão 2.0 ou posterior. Para instalar a versão mais recente, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).
* Um aplicativo que será configurado para usar a chave ou senha que você criará neste artigo. Um aplicativo de exemplo está disponível no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=45343). Para obter instruções, veja o arquivo Leiame incluído.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obtendo ajuda com a interface de linha de comando multiplataforma do Azure

Este artigo pressupõe que você esteja familiarizado com a interface de linha de comando (Bash, Terminal, Prompt de Comando).

O parâmetro --help ou -h pode ser usado para exibir a ajuda de comandos específicos. Como alternativa, o formato [comando] [opções] da ajuda do Azure também pode ser usado. Em caso de dúvida sobre os parâmetros necessários para um comando, consulte a ajuda. Por exemplo, todos os seguintes comandos retornam as mesmas informações:

```azurecli
az account set --help
az account set -h
```

Leia também os artigos a seguir para se familiarizar com o Azure Resource Manager na interface de linha de comando multiplataforma do Azure:

* [Instalar a CLI do Azure.](/cli/azure/install-azure-cli)
* [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Como criar um contêiner protegido (um cofre) no Azure

Os cofres são contêineres protegidos que contam com módulos de segurança de hardware. Os cofres ajudam a reduzir a possibilidade de perda acidental de informações de segurança pela centralização do armazenamento de segredos do aplicativo. Os Key Vaults também controlam e registram o acesso a todas as coisas armazenadas neles. O Azure Key Vault pode tratar da solicitação e da renovação de certificados TLS, fornecendo os recursos necessários para uma solução de gerenciamento de ciclo de vida de certificados robusta. Nas próximas etapas, você criará um cofre.

### <a name="connect-to-your-subscriptions"></a>Conectar-se às suas assinaturas

Para entrar de forma interativa, use o comando a seguir:

```azurecli
az login
```
Para entrar usando uma conta organizacional, você pode passar seu nome de usuário e senha.

```azurecli
az login -u username@domain.com -p password
```

Se você tiver várias assinaturas e precisar especificar uma a ser usada, digite o seguinte para ver as assinaturas da sua conta:

```azurecli
az account list
```

Especifique uma assinatura com o parâmetro de assinatura.

```azurecli
az account set --subscription <subscription name or ID>
```

Para obter mais informações sobre como configurar a Interface de Linha de Comando Multiplataforma do Azure, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Criar um novo grupo de recursos

Ao usar o Gerenciador de Recursos do Azure, todos os recursos relacionados são criados em um grupo de recursos. Crie um cofre de chaves em um grupo de recursos existente. Se você quiser usar um novo grupo de recursos, crie um.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

O primeiro parâmetro é o nome do grupo de recursos e o segundo parâmetro é o local. Para obter uma lista de todos os tipos de locais:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Registrar o provedor de recursos do Cofre de Chaves

 Ao tentar criar um novo cofre de chaves, talvez você veja o erro "A assinatura não está registrada para usar o namespace 'Microsoft.KeyVault'". Se a mensagem aparecer, garanta que o provedor de recursos do Cofre de Chaves está registrado em sua assinatura. Essa operação deve ser executa apenas uma vez para cada assinatura.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Criar um cofre de chave

Use o comando `az keyvault create` para criar um cofre de chave. Esse script tem três parâmetros obrigatórios: um nome do grupo de recursos, um nome do cofre da chave e a localização geográfica.

Para criar um novo cofre de chaves com o nome **ContosoKeyVault**, no grupo de recursos **ContosoResourceGroup**, residindo no local **Ásia Oriental**, digite: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

A saída do comando mostra as propriedades do cofre da chave que você criou. As duas propriedades mais importantes são:

* **nome**: no exemplo, o nome é ContosoKeyVault. Você usará esse nome para outros comandos do Key Vault.
* **vaultUri**: no exemplo, o URI é https://contosokeyvault.vault.azure.net. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Sua conta do Azure agora está autorizada a executar qualquer operação neste cofre de chave. Até o momento, ninguém mais tem autorização.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Adicionar uma chave, segredo ou certificado ao cofre de chaves

Se você quiser que o Azure Key Vault crie uma chave protegida por software para você, use o comando `az key create`.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Se você já tiver uma chave em um arquivo .pem, carregue-a no Azure Key Vault. Você pode escolher proteger a chave com software ou HSM. Este exemplo importa a chave do arquivo .pem e protege-a com o software, usando a senha "hVFkk965BuUv":

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Agora você pode fazer referência à chave que criada ou carregada no Cofre da Chave do Azure, usando o URI. Use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para sempre obter a versão atual. Use https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] para obter essa versão específica. Por exemplo, **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87**. 

Adicione um segredo ao cofre, que é uma senha chamada SQLPassword, com o valor "hVFkk965BuUv" para Azure Key Vaults. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Faça referência a essa senha usando o URI. Use **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obter sempre a versão atual, e https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] para obter essa versão específica. Por exemplo, **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Importe um certificado para o cofre usando um .pem ou .pfx.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Vamos ver a chave, segredo ou certificado criado por você:

* Para exibir suas chaves, digite: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Para exibir seus segredos, digite: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Para exibir os certificados, digite: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Registrar um aplicativo com o Azure Active Directory

Esta etapa geralmente seria feita por um desenvolvedor, em um computador separado. Ela não é específica ao Azure Key Vault, mas é incluída aqui, para fins informativos. Para completar o registro do aplicativo, sua conta, o cofre e o aplicativo precisam estar no mesmo diretório do Azure.

Aplicativos que usam um cofre de chave devem ser autenticados usando um token do Active Directory do Azure.  O proprietário do aplicativo deve registrá-lo primeiro no Azure Active Directory. No final do registro, o proprietário do aplicativo obtém os seguintes valores:

- Uma **ID de Aplicativo** (também conhecida como a appID ou a ID de Cliente do Aplicativo do AAD)
- Uma **chave de autenticação** (também conhecida como o segredo compartilhado). 

O aplicativo deve apresentar esses dois valores ao Active Directory do Azure para obter um token. O modo como um aplicativo é configurado para obter um token dependerá do aplicativo. No [aplicativo de exemplo do Key Vault](https://www.microsoft.com/download/details.aspx?id=45343), o proprietário do aplicativo define esses valores no arquivo app.config.

Para obter etapas detalhadas sobre como registrar um aplicativo com o Azure Active Directory, você deve ler o artigo intitulado [Integrando aplicativos com o Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md), [Usar o portal para criar um aplicativo do Azure Active Directory e uma entidade de serviço que possa acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md) e [Criar uma entidade de serviço do Azure com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

Para registrar um aplicativo no Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Autorizar um aplicativo a usar uma chave ou segredo

Para autorizar o aplicativo a acessar a chave ou o segredo no cofre, use o comando `az keyvault set-policy`.

Por exemplo, se o nome do seu cofre for ContosoKeyVault, o aplicativo tiver um appID de 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, e você quiser autorizar o aplicativo a descriptografar e assinar com chaves em seu cofre, execute o seguinte comando:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Para autorizar o mesmo aplicativo a ler segredos em seu cofre, digite o seguinte comando:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a> Definir as políticas de acesso avançado do cofre de chaves

Use [az keyvault update](/cli/azure/keyvault#az-keyvault-update) para habilitar políticas avançadas para o cofre de chaves.

 Habilitar o Key Vault para a implantação: Permitir que as máquinas virtuais recuperem certificados armazenados como segredos do cofre.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Habilitar o Key Vault para criptografia de disco: exigido ao usar o cofre para criptografia do Azure Disk.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Habilitar o Key Vault para implantação de modelo: Permite que o Resource Manager recupere segredos do cofre.

```azurecli 
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
 ```

## <a name="working-with-hardware-security-modules-hsms"></a>Trabalhar com HSMs (módulos de segurança de hardware)

Para garantia extra, você pode importar ou gerar chaves em HSMs (módulos de segurança de hardware) que nunca deixam os limites do HSM. Os HSMs têm certificação FIPS 140-2 Nível 2. Se esse requisito não se aplicar a você, ignore esta seção e vá para Excluir o cofre de chave e chaves e segredos associados.

Para criar essas chaves protegidas por HSM, você deve ter uma assinatura de cofre que dê suporte a chaves protegidas por HSM.

Ao criar o keyvault, adicione o parâmetro "SKU":

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Você pode adicionar chaves protegidas por software (conforme mostrado anteriormente) e por HSM a este cofre. Para criar uma chave protegida por HSM, defina o parâmetro de Destino como “HSM”:

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

Você pode usar o comando a seguir para importar uma chave de um arquivo PEM para seu computador. Esse comando importa a chave para os HSMs no serviço de Cofre da Chave:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

O próximo comando importa um pacote de BYOK ("traga sua própria chave"). Isso permite gerar sua chave no HSM local e transferi-la para os HSMs no serviço de Cofre da Chave, sem que a chave deixe os limites do HSM:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Para obter instruções mais detalhadas sobre como gerar esse pacote de BYOK, consulte [Como usar Chaves Protegidas por HSM com o Cofre da Chave do Azure](key-vault-hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Excluir o cofre de chave e chaves e segredos associados

Se você não precisar mais do cofre de chaves e das chaves ou segredos contidos nele, poderá excluir o cofre de chaves usando o comando `az keyvault delete`:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Ou você pode excluir um grupo de recursos do Azure inteiro, que inclui o cofre de chave e quaisquer outros recursos incluídos nesse grupo:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Outros comandos da interface de linha de comando de plataforma cruzada do Azure

Outros comandos que podem ser úteis para gerenciar o Azure Key Vault.

Este comando lista uma exibição em tabela de todas as chaves e propriedades selecionadas:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Este comando exibe uma lista completa de propriedades para a chave especificada:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Este comando lista uma exibição em tabela de todos os nomes de segredos e propriedades selecionadas:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Aqui está um exemplo de como remover uma chave específica:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Aqui está um exemplo de como remover um segredo específica:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Próximas etapas

- Para obter a referência completa da CLI do Azure de comandos do cofre de chaves, consulte [referência da CLI do Key Vault](/cli/azure/keyvault).

- Para referências de programação, veja o [Guia do desenvolvedor do Azure Key Vault](key-vault-developers-guide.md)

- Para obter informações sobre o Azure Key Vault e o HSMs, consulte [Como usar chaves protegidas pelo HSM com o Azure Key Vault](key-vault-hsm-protected-keys.md).
