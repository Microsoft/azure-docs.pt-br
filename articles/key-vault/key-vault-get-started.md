---
title: "Introdução ao Cofre de Chaves do Azure | Microsoft Docs"
description: "Use este tutorial para ajudá-lo a começar a usar o Cofre da Chave do Azure para criar um contêiner de proteção avançado no Azure, para armazenar e gerenciar chaves de criptografia e segredos no Azure."
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 36721e1d-38b8-4a15-ba6f-14ed5be4de79
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/16/2017
ms.author: barclayn
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: 73b4ae4b7baca434c6aed99a2e59a9102b0d96ed
ms.contentlocale: pt-br
ms.lasthandoff: 07/22/2017

---
# <a name="get-started-with-azure-key-vault"></a>Introdução ao Cofre da Chave do Azure
O Cofre da Chave do Azure está disponível na maioria das regiões. Para obter mais informações, consulte a [Página de preços do Cofre da Chave](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introdução
Use este tutorial para ajudá-lo a começar a usar o Cofre da Chave do Azure para criar um contêiner de proteção avançado (um cofre) no Azure, para armazenar e gerenciar chaves de criptografia e segredos no Azure. Ele percorre o processo de uso do Azure PowerShell para criar um cofre que contém uma chave ou senha que você pode usar com um aplicativo do Azure. Em seguida, ele mostra como um aplicativo pode usar essa chave ou senha.

**Tempo estimado para conclusão:** 20 minutos

> [!NOTE]
> Este tutorial não inclui instruções sobre como escrever o aplicativo do Azure que uma das etapas inclui, ou seja, como autorizar um aplicativo para usar uma chave ou um segredo no cofre da chave.
>
> Este tutorial usa o Azure PowerShell. Para obter instruções de interface de linha de comando entre diferentes plataformas, consulte [este tutorial equivalente](key-vault-manage-with-cli2.md).
>
>

Para obter informações gerais sobre o Cofre da Chave do Azure, consulte [O que é o Cofre da Chave do Azure?](key-vault-whatis.md)

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, você precisará do seguinte:

* Uma assinatura do Microsoft Azure. Se você não tiver uma, poderá se inscrever em uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Azure PowerShell, **versão mínima: 1.1.0**. Para instalar o Azure PowerShell e associá-lo à sua assinatura do Azure, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Se você já tiver instalado o Azure PowerShell e não souber a versão, no console do Azure PowerShell, digite `(Get-Module azure -ListAvailable).Version`. Quando você tiver o Azure PowerShell versão 0.9.1 até 0.9.8 instalado, ainda poderá usar este tutorial com algumas pequenas alterações. Por exemplo, você deverá usar o comando `Switch-AzureMode AzureResourceManager` ; alguns dos comandos do Cofre da Chave do Azure foram alterados. Para obter uma lista dos cmdlets do Cofre de Chaves para as versões 0.9.1 até 0.9.8, consulte [Cmdlets do Cofre de Chaves do Azure](/powershell/module/azurerm.keyvault/#key_vault).
* Um aplicativo que será configurado para usar a chave ou senha que você criará neste tutorial. Um aplicativo de exemplo está disponível no [Centro de Download da Microsoft](http://www.microsoft.com/en-us/download/details.aspx?id=45343). Para obter instruções, consulte o arquivo Leiame.

Este tutorial foi criado para iniciantes do Azure PowerShell, mas pressupõe que você compreende os conceitos básicos, como módulos, cmdlets e sessões. Para obter mais informações, consulte [Introdução ao Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx).

Para obter ajuda detalhada sobre qualquer cmdlet que você vir neste tutorial, use o cmdlet **Get-Help** .

    Get-Help <cmdlet-name> -Detailed

Por exemplo, para obter ajuda para o cmdlet **AzureRmAccount Login** , digite:

    Get-Help Login-AzureRmAccount -Detailed

Leia também os tutoriais a seguir para se familiarizar com o Gerenciador de Recursos do Azure no Azure PowerShell:

* [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview)
* [Usando o PowerShell do Azure com o Gerenciador de Recursos](../powershell-azure-resource-manager.md)

## <a id="connect"></a>Conectar-se a suas assinaturas
Inicie uma sessão do PowerShell do Azure e entre em sua conta do Azure com o seguinte comando:  

    Login-AzureRmAccount

Observe que, se você estiver usando uma instância específica do Azure, por exemplo, o Azure Governamental, deve usar o parâmetro -Environment com o comando. Por exemplo: `Login-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)`

Na janela pop-up do navegador, insira o nome de usuário e a senha da sua conta do Azure. O Azure PowerShell obtém todas as assinaturas que estão associadas a essa conta e, por padrão, usa a primeira.

Se você tem várias assinaturas e quer especificar uma a ser usada para o Cofre de Chaves do Azure, digite o seguinte para ver as assinaturas da sua conta:

    Get-AzureRmSubscription

Depois, para especificar a assinatura a ser usada, digite:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Para saber mais sobre a configuração PowerShell do Azure, consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

## <a id="resource"></a>Criar um novo grupo de recursos
Quando você usa o Gerenciador de Recursos do Azure, todos os recursos relacionados são criados dentro de um grupo de recursos. Nós criaremos um novo grupo de recursos chamado **ContosoResourceGroup** para este tutorial:

    New-AzureRmResourceGroup –Name 'ContosoResourceGroup' –Location 'East Asia'


## <a id="vault"></a>Criar um cofre de chave
Use o cmdlet [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) para criar um cofre de chaves. Esse cmdlet tem três parâmetros obrigatórios: um **nome do grupo de recursos**, um **nome do cofre de chaves** e a **localização geográfica**.

Por exemplo, se você usar o nome do cofre **ContosoKeyVault**, o nome do grupo de recursos **ContosoResourceGroup** e a localização **Ásia Oriental**, digite:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

A saída deste cmdlet mostra as propriedades do cofre de chave que você acabou de criar. As duas propriedades mais importantes são:

* **Nome do Cofre**: no exemplo, isso é **ContosoKeyVault**. Você usará esse nome para outros cmdlets do Cofre da Chave.
* **URI do Cofre**: no exemplo, isso é https://contosokeyvault.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Sua conta do Azure agora está autorizada a executar qualquer operação neste cofre de chave. Até o momento, ninguém mais está.

> [!NOTE]
> Se você vir o erro **A assinatura não está registrada para usar o namespace 'Microsoft.KeyVault'** ao tentar criar o novo cofre de chaves, execute `Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"`, em seguida, execute novamente o comando New-AzureRmKeyVault. Para obter mais informações, consulte [Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider).
>
>

## <a id="add"></a>Adicionar uma chave ou segredo ao cofre de chave
Se você quiser que o Cofre de Chaves do Azure crie uma chave protegida por software para você, use o cmdlet [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) e digite o seguinte:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -Destination 'Software'

No entanto, se você tiver uma chave protegida por software existente em um arquivo .PFX salvo na unidade C:\ em um arquivo denominado softkey.pfx que você deseja carregar no Cofre de Chaves do Azure, digite o seguinte para definir a variável **securepfxpwd** para uma senha **123** do arquivo .PFX:

    $securepfxpwd = ConvertTo-SecureString –String '123' –AsPlainText –Force

Depois, digite o seguinte para importar a chave do arquivo .PFX, que protege a chave com um software no serviço de Cofre da Chave:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd


Agora, você pode fazer referência à chave que foi criada ou carregada no Cofre da Chave do Azure, usando seu URI. Use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para sempre obter a versão atual e use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** para obter esta versão específica.  

Para exibir o URI dessa chave, digite:

    $Key.key.kid

Para adicionar um segredo ao cofre, que é uma senha chamada SQLPassword e que tem o valor Pa$$w0rd no Cofre da Chave do Azure, converta o valor Pa$$w0rd para uma cadeia de caracteres segura digitando o seguinte:

    $secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force

Em seguida, digite o seguinte:

    $secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword' -SecretValue $secretvalue

Agora, você pode fazer referência a essa senha que foi adicionada ao Cofre da Chave do Azure usando seu URI. Use **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para sempre obter a versão atual e use **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** para obter esta versão específica.

Para exibir o URI do segredo, digite:

    $secret.Id

Vejamos a chave ou o segredo que você acabou de criar:

* Para exibir a chave, digite: `Get-AzureKeyVaultKey –VaultName 'ContosoKeyVault'`
* Para exibir o segredo, digite: `Get-AzureKeyVaultSecret –VaultName 'ContosoKeyVault'`

Agora, seu cofre de chave e a chave ou o segredo estão prontos para serem usados pelos aplicativos. Você precisa autorizar os aplicativos a usá-los.  

## <a id="register"></a>Registrar um aplicativo com o Active Directory do Azure
Esta etapa geralmente seria feita por um desenvolvedor, em um computador separado. Ela não é específica ao Cofre da Chave do Azure, mas é incluída aqui para que as informações fiquem completas.

> [!IMPORTANT]
> Para concluir o tutorial, sua conta, o cofre e o aplicativo que você registrará nesta etapa devem todos estar no mesmo diretório do Azure.
>
>

Aplicativos que usam um cofre de chave devem ser autenticados usando um token do Active Directory do Azure. Para fazer isso, o proprietário do aplicativo deve primeiro registrar o aplicativo no seu Active Directory do Azure. No final do registro, o proprietário do aplicativo obtém os seguintes valores:

* Uma **ID do Aplicativo** (também conhecida como ID do Cliente) e a **chave de autenticação** (também conhecida como segredo compartilhado). O aplicativo deve apresentar esses dois valores ao Active Directory do Azure para obter um token. Como o aplicativo é configurado para fazer isso depende do aplicativo. Para o aplicativo de exemplo do Cofre da Chave, o proprietário do aplicativo define esses valores no arquivo app.config.

Para registrar seu aplicativo com o Active Directory do Azure:

1. Entre no portal clássico do Azure.
2. À esquerda, clique em **Active Directory**e selecione o diretório no qual você registrará o aplicativo. <br> <br> **Observação:** você deve selecionar o mesmo diretório que contém a assinatura do Azure com a qual você criou o cofre de chaves. Se você não souber qual é o diretório, clique em **Configurações**, identifique a assinatura com a qual você criou o cofre de chaves e anote o nome do diretório exibido na última coluna.
3. Clique em **APLICATIVOS**. Se nenhum aplicativo tiver sido adicionado ao seu diretório, essa página mostrará somente o link **Adicionar um aplicativo** . Clique no link ou, se preferir, em **ADICIONAR** na barra de comandos.
4. No assistente **ADICIONAR APLICATIVO** na página **O que você deseja fazer?**, clique em **Adicionar um aplicativo que minha organização está desenvolvendo**.
5. Na página **Conte-nos sobre seu aplicativo**, especifique um nome para seu aplicativo, em seguida, selecione **APLICATIVO WEB E/OU API WEB** (o padrão). Clique no ícone **Avançar** .
6. Na página **Propriedades do aplicativo**, especifique a **URL DE LOGON** e o **URI DA ID DO aplicativo** para seu aplicativo Web. Se seu aplicativo não tiver esses valores, você pode inventá-los para esta etapa (por exemplo, você poderia especificar http://test1.contoso.com para as duas caixas). Não importa se os sites existem. O importante é que o URI de ID de cada aplicativo é diferente para todos os aplicativos no diretório. O diretório usa essa cadeia de caracteres para identificar seu aplicativo.
7. Clique no ícone **Concluído** para salvar suas alterações no assistente.
8. Na página **Início Rápido**, clique em **CONFIGURAR**.
9. Role até a seção **chaves**, selecione a duração, em seguida, clique em **SALVAR**. A página é atualizada e passa a mostrar um valor de chave. Você deve configurar seu aplicativo com esse valor de chave e o valor da **ID DO CLIENTE** . (As instruções para esta configuração são específicas do aplicativo.)
10. Copie o valor da ID do cliente desta página, que você usará na próxima etapa para definir as permissões no cofre.

## <a id="authorize"></a>Autorizar o aplicativo a usar a chave ou segredo
Para autorizar o aplicativo para acessar a chave ou o segredo no cofre, use o cmdlet [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy).

Por exemplo, se o nome do cofre for **ContosoKeyVault** e o aplicativo que você quer autorizar tiver a ID de cliente 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, e você quiser autorizar o aplicativo a descriptografar e assinar com chaves em seu cofre, execute o seguinte:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign

Se você deseja autorizar que o mesmo aplicativo leia segredos em seu cofre, execute o seguinte:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToSecrets Get

## <a id="HSM"></a>Se quiser usar um HSM (módulo de segurança de hardware)
Para garantia extra, você pode importar ou gerar chaves em HSMs (módulos de segurança de hardware) que nunca deixam os limites do HSM. Os HSMs têm certificação FIPS 140-2 Nível 2. Se esse requisito não se aplicar a você, ignore esta seção e vá para [Excluir o cofre de chave e chaves e segredos associados](#delete).

Para criar essas chaves protegidas por HSM, você deverá usar a [Camada de serviço Premium do Cofre de Chaves do Azure para dar suporte a chaves protegidas por HSM](https://azure.microsoft.com/pricing/free-trial/). Além disso, observe que essa funcionalidade não está disponível para o Azure China.

Ao criar o cofre de chaves, adicione o parâmetro **-SKU**:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVaultHSM' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -SKU 'Premium'



Você pode adicionar chaves protegidas por software (conforme mostrado anteriormente) e por HSM a este cofre de chaves. Para criar uma chave protegida por HSM, defina o parâmetro **-Destination** como 'HSM':

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -Destination 'HSM'

Você pode usar o comando a seguir para importar uma chave de um arquivo .PFX para seu computador. Esse comando importa a chave para os HSMs no serviço de Cofre da Chave:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\softkey.pfx' -KeyFilePassword $securepfxpwd -Destination 'HSM'


O próximo comando importa um pacote de BYOK ("traga sua própria chave"). Esse cenário permite gerar sua chave no HSM local e transferi-la para os HSMs no serviço de Cofre de Chaves, sem que a chave deixe os limites do HSM:

    $key = Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMKey' -KeyFilePath 'c:\ITByok.byok' -Destination 'HSM'

Para obter instruções detalhadas sobre como gerar esse pacote BYOK, confira [Como gerar e transferir chaves protegidas por HSM para o Cofre da Chave do Azure](key-vault-hsm-protected-keys.md).

## <a id="delete"></a>Excluir o cofre de chave e as chaves e segredos associados
Se você não precisar mais do cofre de chaves e da chave ou segredo que ele contém, poderá excluir o cofre de chaves usando o cmdlet [Remove-AzureRmKeyVault](/powershell/module/azurerm.keyvault/remove-azurermkeyvault):

    Remove-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Ou você pode excluir um grupo de recursos do Azure inteiro, que inclui o cofre de chave e quaisquer outros recursos incluídos nesse grupo:

    Remove-AzureRmResourceGroup -ResourceGroupName 'ContosoResourceGroup'


## <a id="other"></a>Outros cmdlets do PowerShell do Azure
Outros comandos que podem ser úteis para gerenciar o Cofre da Chave do Azure.

* `$Keys = Get-AzureKeyVaultKey -VaultName 'ContosoKeyVault'`: este comando obtém uma exibição em tabela de todas as chaves e propriedades selecionadas.
* `$Keys[0]`: este comando exibe uma lista completa de propriedades para a chave especificada
* `Get-AzureKeyVaultSecret`: este comando lista uma exibição em tabela de todos os nomes de segredos e propriedades selecionadas.
* `Remove-AzureKeyVaultKey -VaultName 'ContosoKeyVault' -Name 'ContosoFirstKey'`: exemplo de como remover uma chave específica.
* `Remove-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'SQLPassword'`: exemplo de como remover um segredo específico.

## <a id="next"></a>Próximas etapas
Para um tutorial de acompanhamento que usa o Cofre da Chave do Azure em um aplicativo Web, confira [Usar o Cofre da Chave do Azure em um Aplicativo Web](key-vault-use-from-web-application.md).

Para ver como o cofre de chaves está sendo usado, confira [Log do Cofre de Chaves do Azure](key-vault-logging.md).

Para obter uma lista dos cmdlets do Azure PowerShell mais recentes para o Cofre de Chaves do Azure, consulte [Cmdlets do Cofre de Chaves do Azure](/powershell/module/azurerm.keyvault/#key_vault).

Para referências de programação, consulte [Guia do desenvolvedor do Cofre da Chave do Azure](key-vault-developers-guide.md).

