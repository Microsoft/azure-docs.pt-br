---
title: Gerenciar Azure Key Vault usando CLI | Microsoft Docs
description: Use este tutorial para automatizar tarefas comuns no Key Vault usando a CLI 2.0
services: key-vault
documentationcenter: 
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: ambapat
ms.openlocfilehash: 5da9f5eceda71ac85259193e0f183c72813e1679
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-key-vault-using-cli-20"></a>Gerenciar o Key Vault usando a CLI 2.0
O Cofre da Chave do Azure está disponível na maioria das regiões. Para obter mais informações, consulte a [Página de preços do Cofre da Chave](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introdução
Use este tutorial para ajudá-lo a começar a usar o Cofre da Chave do Azure para criar um contêiner de proteção avançado (um cofre) no Azure, para armazenar e gerenciar chaves de criptografia e segredos no Azure. Ele mostra o passo a passo do processo de uso da interface de linha de comando de plataforma cruzada do Azure para criar um cofre que contém uma chave ou senha que você pode usar com um aplicativo do Azure. Em seguida, ele mostra como um aplicativo pode usar essa chave ou senha.

**Tempo estimado para conclusão:** 20 minutos

> [!NOTE]
> Este tutorial não inclui instruções sobre como escrever o aplicativo do Azure incluído em uma das etapas, que mostra como autorizar um aplicativo a usar uma chave ou um segredo do cofre da chave.
>
> Este tutorial usa a última CLI 2.0 do Azure.
>
>

Para obter informações gerais sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará do seguinte:

* Uma assinatura do Microsoft Azure. Se não tiver uma assinatura, você pode se inscrever para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* Interface de Linha de Comando versão 2.0 ou posterior. Para instalar a última versão e conectá-la à sua assinatura do Azure, consulte [Instalar e configurar a Interface de Linha de Comando 2.0 de plataforma cruzada do Azure](/cli/azure/install-azure-cli).
* Um aplicativo que será configurado para usar a chave ou senha que você criará neste tutorial. Um aplicativo de exemplo está disponível no [Centro de Download da Microsoft](http://www.microsoft.com/download/details.aspx?id=45343). Para obter instruções, consulte o arquivo Leiame.

## <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obtendo ajuda com a interface de linha de comando de plataforma cruzada do Azure
Este tutorial pressupõe que você esteja familiarizado com a interface de linha de comando (Bash, Terminal, Prompt de Comando)

O parâmetro --help ou -h pode ser usado para exibir a ajuda de comandos específicos. Como alternativa, o formato azure help [comando] [opções] também pode ser usado para retornar as mesmas informações. Por exemplo, todos os seguintes comandos retornam as mesmas informações:

```
az account set --help
az account set -h
```

Em caso de dúvida sobre os parâmetros necessários para um comando, consulte a ajuda usando --help, -h ou az help [comando].

Você pode ler também os tutoriais a seguir para se familiarizar com o Gerenciador de Recursos do Azure na interface de linha de comando da plataforma cruzada do Azure:

* [Instalar a CLI do Azure.](/cli/azure/install-azure-cli)
* [Introdução à CLI do Azure 2.0](/cli/azure/get-started-with-azure-cli)

## <a name="connect-to-your-subscriptions"></a>Conectar-se às suas assinaturas
Para fazer logon usando uma conta institucional, use o seguinte comando:

```
az login -u username@domain.com -p password
```

ou se deseja fazer logon digitando interativamente

```
az login
```

Se você tiver várias assinaturas e quiser especificar uma a ser usada para o Cofre da Chave do Azure, digite o seguinte para ver as assinaturas da sua conta:

```
az account list
```

Depois, para especificar a assinatura a ser usada, digite:

```
az account set --subscription <subscription name or ID>
```

Para obter mais informações sobre como configurar a Interface de Linha de Comando de plataforma cruzada do Azure, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-new-resource-group"></a>Criar um novo grupo de recursos
Ao usar o Gerenciador de Recursos do Azure, todos os recursos relacionados são criados em um grupo de recursos. Criaremos um novo grupo de recursos “ContosoResourceGroup” para este tutorial.

```
az group create -n 'ContosoResourceGroup' -l 'East Asia'
```

O primeiro parâmetro é o nome do grupo de recursos e o segundo parâmetro é o local. Para o local, use o comando `az account list-locations` para identificar como especificar um local alternativo ao deste exemplo. Se precisar de mais informações, digite: `az account list-locations -h`.

## <a name="register-the-key-vault-resource-provider"></a>Registrar o provedor de recursos do Cofre de Chaves
Verifique se o provedor de recursos do Cofre de Chaves está registrado em sua assinatura:

```
az provider register -n Microsoft.KeyVault
```

Isso só precisa ser feito uma vez por assinatura.

## <a name="create-a-key-vault"></a>Criar um cofre de chave
Use o comando `az keyvault create` para criar um cofre de chave. Esse script tem três parâmetros obrigatórios: um nome do grupo de recursos, um nome do cofre da chave e a localização geográfica.

Por exemplo, se você usar o nome de cofre ContosoKeyVault, o nome do grupo de recursos ContosoResourceGroup e o local Ásia Oriental, digite:
```
az keyvault create --name 'ContosoKeyVault' --resource-group 'ContosoResourceGroup' --location 'East Asia'
```

A saída do comando mostra as propriedades do cofre da chave que você acabou de criar. As duas propriedades mais importantes são:

* **name**: no exemplo, é ContosoKeyVault. Você usará esse nome para outros comandos do Key Vault.
* **vaultUri**: no exemplo, isso é https://contosokeyvault.vault.azure.net. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Sua conta do Azure agora está autorizada a executar qualquer operação neste cofre de chave. Até o momento, ninguém mais está.

## <a name="add-a-key-or-secret-to-the-key-vault"></a>Adicionar uma chave ou segredo ao cofre da chave
Se você quiser que o Cofre da Chave do Azure crie uma chave protegida por software para você, use o comando `az key create` e digite o seguinte:
```
az keyvault key create --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --protection software
```
No entanto, se você tiver uma chave existente em um arquivo PEM salvo como arquivo local em um arquivo chamado softkey.pem que você deseja carregar no Cofre da Chave do Azure, digite o seguinte para importar a chave do arquivo PEM, o qual protege a chave pelo software no serviço de Cofre da Chave:
```
az keyvault key import --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey' --pem-file './softkey.pem' --pem-password 'PaSSWORD' --protection software
```
Agora você pode fazer referência à chave que criada ou carregada no Cofre da Chave do Azure, usando o URI. Use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para sempre obter a versão atual e use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obter esta versão específica.

Para adicionar um segredo ao cofre, que é uma senha chamada SQLPassword e que tem o valor Pa$$w0rd no Cofre da Chave do Azure, digite o seguinte:
```
az keyvault secret set --vault-name 'ContosoKeyVault' --name 'SQLPassword' --value 'Pa$$w0rd'
```
Agora, você pode fazer referência a essa senha que foi adicionada ao Cofre da Chave do Azure usando seu URI. Use **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para sempre obter a versão atual e use **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obter esta versão específica.

Vamos exibir a chave ou o segredo que você acabou de criar:

* Para exibir a chave, digite: `az keyvault key list --vault-name 'ContosoKeyVault'`
* Para exibir o segredo, digite: `az keyvault secret list --vault-name 'ContosoKeyVault'`

## <a name="register-an-application-with-azure-active-directory"></a>Registrar um aplicativo com o Active Directory do Azure
Esta etapa geralmente seria feita por um desenvolvedor, em um computador separado. Ela não é específica ao Cofre da Chave do Azure, mas é incluída aqui para que as informações fiquem completas.

> [!IMPORTANT]
> Para concluir o tutorial, sua conta, o cofre e o aplicativo que você registrará nesta etapa devem todos estar no mesmo diretório do Azure.
>
>

Aplicativos que usam um cofre de chave devem ser autenticados usando um token do Active Directory do Azure. Para fazer isso, o proprietário do aplicativo deve primeiro registrar o aplicativo no seu Active Directory do Azure. No final do registro, o proprietário do aplicativo obtém os seguintes valores:

* Uma **ID do Aplicativo** (também conhecida como ID do Cliente) e a **chave de autenticação** (também conhecida como segredo compartilhado). O aplicativo deve apresentar esses dois valores ao Active Directory do Azure para obter um token. Como o aplicativo é configurado para fazer isso depende do aplicativo. Para o aplicativo de exemplo do Cofre da Chave, o proprietário do aplicativo define esses valores no arquivo app.config.

Para registrar seu aplicativo com o Active Directory do Azure:

1. Entre no Portal do Azure.
2. À esquerda, clique em **Azure Active Directory** e selecione o diretório no qual você registrará o aplicativo. <br> <br> 

> [!Note] 
> Você deve selecionar o mesmo diretório que contém a assinatura do Azure com a qual você criou o cofre de chaves. Se você não souber qual é o diretório, clique em **Configurações**, identifique a assinatura com a qual você criou o cofre de chave e anote o nome do diretório exibido na última coluna.

3. Clique em **APLICATIVOS**. Se nenhum aplicativo tiver sido adicionado ao seu diretório, essa página mostrará somente o link **Adicionar um Aplicativo** . Clique no link ou em **ADICIONAR** na barra de comandos.
4. No assistente **ADICIONAR APLICATIVO** na página **O que você deseja fazer?**, clique em **Adicionar um aplicativo que minha organização está desenvolvendo**.
5. Na página **Conte-nos sobre seu aplicativo**, especifique um nome para seu aplicativo e selecione **APLICATIVO WEB E/OU API WEB** (o padrão). Clique no ícone Avançar.
6. Na página **Propriedades do aplicativo**, especifique a **URL DE LOGON** e o **URI DA ID DO aplicativo** para seu aplicativo Web. Se seu aplicativo não tiver esses valores, você pode inventá-los para esta etapa (por exemplo, você poderia especificar http://test1.contoso.com para as duas caixas). Não importa se os sites existem, o importante é que o URI de ID de cada aplicativo é diferente para todos os aplicativos no diretório. O diretório usa essa cadeia de caracteres para identificar seu aplicativo.
7. Clique no ícone Concluído para salvar suas alterações no assistente.
8. Na página de Início Rápido, clique em **CONFIGURAR**.
9. Role até a seção **chaves**, selecione a duração, em seguida, clique em **SALVAR**. A página é atualizada e passa a mostrar um valor de chave. Você deve configurar seu aplicativo com esse valor de chave e o valor da **ID DO CLIENTE** . (As instruções para esta configuração são específicas do aplicativo).
10. Copie o valor da ID do cliente desta página, que você usará na próxima etapa para definir as permissões no cofre.

## <a name="authorize-the-application-to-use-the-key-or-secret"></a>Autorizar o aplicativo a usar a chave ou o segredo
Para autorizar o aplicativo a acessar a chave ou o segredo no cofre, use o comando `az keyvault set-policy`.

Por exemplo, se o nome do cofre for ContosoKeyVault e o aplicativo que você quer autorizar tiver a ID de cliente 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, e você quiser autorizar o aplicativo a descriptografar e assinar com chaves em seu cofre. Em seguida, execute o seguinte:
```
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Se você deseja autorizar que o mesmo aplicativo leia segredos em seu cofre, execute o seguinte:
```
az keyvault set-policy --name 'ContosoKeyVault' --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```
## <a name="if-you-want-to-use-a-hardware-security-module-hsm"></a>Se quiser usar um HSM (módulo de segurança de hardware)
Para garantia extra, você pode importar ou gerar chaves em HSMs (módulos de segurança de hardware) que nunca deixam os limites do HSM. Os HSMs têm certificação FIPS 140-2 Nível 2. Se esse requisito não se aplicar a você, ignore esta seção e vá para [Excluir o cofre de chave e chaves e segredos associados](#delete-the-key-vault-and-associated-keys-and-secrets).

Para criar essas chaves protegidas por HSM, você deve ter uma assinatura de cofre que dê suporte a chaves protegidas por HSM.

Ao criar o keyvault, adicione o parâmetro "SKU":

```
az keyvault create --name 'ContosoKeyVaultHSM' --resource-group 'ContosoResourceGroup' --location 'East Asia' --sku 'Premium'
```
Você pode adicionar chaves protegidas por software (conforme mostrado anteriormente) e por HSM a este cofre. Para criar uma chave protegida por HSM, defina o parâmetro de Destino como “HSM”:

```
az keyvault key create --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --protection 'hsm'
```

Você pode usar o comando a seguir para importar uma chave de um arquivo PEM para seu computador. Esse comando importa a chave para os HSMs no serviço de Cofre da Chave:

```
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --pem-file '/.softkey.pem' --protection 'hsm' --pem-password 'PaSSWORD'
```
O próximo comando importa um pacote de BYOK ("traga sua própria chave"). Isso permite gerar sua chave no HSM local e transferi-la para os HSMs no serviço de Cofre da Chave, sem que a chave deixe os limites do HSM:

```
az keyvault key import --vault-name 'ContosoKeyVaultHSM' --name 'ContosoFirstHSMKey' --byok-file './ITByok.byok' --protection 'hsm'
```
Para obter instruções mais detalhadas sobre como gerar esse pacote de BYOK, consulte [Como usar Chaves Protegidas por HSM com o Cofre da Chave do Azure](key-vault-hsm-protected-keys.md).

## <a name="delete-the-key-vault-and-associated-keys-and-secrets"></a>Excluir o cofre de chave e chaves e segredos associados
Se você não precisar mais do cofre de chaves e da chave ou do segredo contido nela, poderá excluir o cofre de chaves usando o comando `az keyvault delete`:

```
az keyvault delete --name 'ContosoKeyVault'
```

Ou você pode excluir um grupo de recursos do Azure inteiro, que inclui o cofre de chave e quaisquer outros recursos incluídos nesse grupo:

```
az group delete --name 'ContosoResourceGroup'
```

## <a name="other-azure-cross-platform-command-line-interface-commands"></a>Outros comandos da interface de linha de comando de plataforma cruzada do Azure
Outros comandos que podem ser úteis para gerenciar o Cofre da Chave do Azure.

Este comando lista uma exibição em tabela de todas as chaves e propriedades selecionadas:

az keyvault key list --vault-name 'ContosoKeyVault'

Este comando exibe uma lista completa de propriedades para a chave especificada:

az keyvault key show --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'

Este comando lista uma exibição em tabela de todos os nomes de segredos e propriedades selecionadas:

az keyvault secret list --vault-name 'ContosoKeyVault'

Aqui está um exemplo de como remover uma chave específica:

az keyvault key delete --vault-name 'ContosoKeyVault' --name 'ContosoFirstKey'

Aqui está um exemplo de como remover um segredo específica:

az keyvault secret delete --vault-name 'ContosoKeyVault' --name 'SQLPassword'


## <a name="next-steps"></a>Próximas etapas
Para obter a referência completa da CLI do Azure de comandos do cofre de chaves, consulte [referência da CLI do Key Vault](/cli/azure/keyvault)

Para referências de programação, consulte [Guia do desenvolvedor do Cofre da Chave do Azure](key-vault-developers-guide.md).
