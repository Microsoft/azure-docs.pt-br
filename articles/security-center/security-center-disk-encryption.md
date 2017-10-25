---
title: "Criptografar uma máquina virtual do Azure | Microsoft Docs"
description: "Este documento ajudará você a criptografar uma Máquina Virtual do Azure depois de receber um alerta da Central de Segurança do Azure."
services: security, security-center
documentationcenter: na
author: TomShinder
manager: swadhwa
editor: 
ms.assetid: f6c28bc4-1f79-4352-89d0-03659b2fa2f5
ms.service: security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2017
ms.author: tomsh
ms.openlocfilehash: 8d39aafb0ab7b0e87afdf4d2f50f1e224b8d251f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="encrypt-an-azure-virtual-machine"></a>Criptografar uma Máquina Virtual do Azure
A Central de Segurança do Azure alertará você se houver máquinas virtuais que não estejam criptografadas. Esses alertas serão mostrados como Alta Severidade e a recomendação é criptografar essas máquinas virtuais.

![Recomendação de criptografia de disco](./media/security-center-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> As informações neste documento se aplicam à criptografia de máquinas virtuais sem usar uma Chave de Criptografia de Chave (que é necessária para fazer backup de máquinas virtuais usando o Backup do Azure). Confira o artigo [Azure Disk Encryption para máquinas virtuais do Azure do Windows e do Linux](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption) para obter informações sobre como usar uma Chave de Criptografia de Chave para dar suporte ao Backup do Azure para Máquinas Virtuais do Azure criptografadas.
>
>

Para criptografar as Máquinas Virtuais do Azure identificadas pela Central de Segurança do Azure como precisando de criptografia, recomendamos as seguintes etapas:

* Instalar e configurar o PowerShell do Azure. Isso permitirá que você execute os comandos do PowerShell necessários para a configuração dos pré-requisitos exigidos para criptografar as Máquinas Virtuais do Azure.
* Obter e executar o script Pré-requisitos de Azure Disk Encryption do Azure PowerShell
* Criptografar suas máquinas virtuais

O objetivo deste documento é permitir que você criptografe suas máquinas virtuais, mesmo se tiver pouca ou nenhuma experiência com o Azure PowerShell.
Este documento presume que você esteja usando o Windows 10 como o computador cliente do qual configurará a Azure Disk Encryption.

Há várias abordagens que podem ser usadas para configurar os pré-requisitos e para configurar a criptografia para Máquinas Virtuais do Azure. Se você já estiver bem familiarizado com o Azure PowerShell ou a CLI do Azure, poderá preferir usar abordagens alternativas.

> [!NOTE]
> Para saber mais sobre as abordagens alternativas para configurar a criptografia para as máquinas virtuais do Azure, confira [Azure Disk Encryption para Máquinas Virtuais do Azure do Windows e do Linux](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0).
>
>

## <a name="install-and-configure-azure-powershell"></a>Instalar e configurar o PowerShell do Azure
Você precisa do Azure PowerShell versão 1.2.1, ou superior, instalado no computador. O artigo [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) contém todas as etapas necessárias para provisionar o computador para trabalhar com o Azure PowerShell. A abordagem mais simples é usar a abordagem de instalação do Web PI mencionado no artigo. Mesmo que você já tenha instalado o Azure PowerShell, instale novamente usando a abordagem do Web PI para que você tenha a versão mais recente do PowerShell do Azure.

## <a name="obtain-and-run-the-azure-disk-encryption-prerequisites-configuration-script"></a>Obtenha e execute o script de configuração de pré-requisitos de criptografia de disco do Azure.
O Script de Configuração de Pré-requisitos de Azure Disk Encryption configurará todos os pré-requisitos necessários para a criptografia de suas Máquinas Virtuais do Azure.

1. Acesse a página do GitHub com o [Script de Configuração de Pré-requisito de Azure Disk Encryption](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
2. Na página do GitHub, clique no botão **Bruto** .
3. Use **CTRL-A** para selecionar todo o texto na página e use **CTRL-C** para copiar todo o texto da página para a área de transferência.
4. Abra o **Bloco de Notas** e cole nele o texto copiado.
5. Crie uma nova pasta na unidade C: denominada **AzureADEScript**.
6. Salve o arquivo do Bloco de Notas. Clique em **Arquivo** e em **Salvar como**. Na caixa Nome do arquivo, digite **"ADEPrereqScript.ps1"** e clique em **Salvar**. (coloque aspas ao redor do nome, caso contrário, ele salvará o arquivo com uma extensão .txt).

Agora que o conteúdo do script está salvo, abra o script no ISE do PowerShell:

1. No Menu Iniciar, clique em **Cortana**. Solicite “PowerShell” à **Cortana** digitando **PowerShell** na caixa de texto de pesquisa da Cortana.
2. Clique com o botão direito do mouse em **ISE do Windows PowerShell** e clique em **Executar como administrador**.
3. Na janela **Administrador: ISE do Windows PowerShell**, clique em **Exibir** e clique em **Mostrar Painel de Script**.
4. Se você vir o painel **Comandos** no lado direito da janela, clique no **"x"** no canto superior direito do painel para fechá-lo. Se o texto for muito pequeno para visualização, use **CTRL+Adicionar** ("Adicionar" é o sinal "+"). Se o texto for muito grande, use **CTRL+Subtrair** (Subtrair é o sinal "-").
5. Clique em **Arquivo** e clique em **Abrir**. Navegue até a pasta **C:\AzureADEScript** e clique duas vezes no **ADEPrereqScript**.
6. O conteúdo de **ADEPrereqScript** já deve aparecer no ISE do PowerShell e está codificado por cor para ajudar você a ver diversos componentes, como comandos, parâmetros e variáveis com maior facilidade.

Agora você verá algo como a figura abaixo.

![Janela do ISE do PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig2.png)

O painel superior é conhecido como o "painel de script" e o painel inferior é conhecido como "console". Usaremos esses termos neste artigo.

## <a name="run-the-azure-disk-encryption-prerequisites-powershell-command"></a>Executar o comando do PowerShell de pré-requisitos de criptografia de disco do Azure
O script Pré-requisitos de Azure Disk Encryption solicitará as informações a seguir depois de iniciar o script:

* **Nome do Grupo de Recursos** - nome do Grupo de Recursos no qual você deseja colocar o Cofre da Chave.  Um novo Grupo de Recursos com o nome inserido será criado caso ainda não haja um com esse nome. Se você já tiver um Grupo de Recursos que deseja usar nesta assinatura, insira o nome desse Grupo de Recursos.
* **Nome do Cofre da Chave** - nome do Cofre da Chave no qual as chaves de criptografia devem ser colocadas. Um novo Cofre da Chave com esse nome será criado caso você ainda não tenha um com esse nome. Se você já tiver um Cofre da Chave que deseja usar, insira o nome do cofre existente.
* **Local** - o local do Cofre da Chave. Verifique se o Cofre da Chave e as VMs a serem criptografados estão no mesmo local. Se você não souber o local, há etapas posteriores neste artigo que mostram como encontrá-lo.
* **Nome do Aplicativo do Active Directory do Azure** - nome do aplicativo do Azure Active Directory que será usado para gravar segredos no Cofre da Chave. Será criado um novo aplicativo com esse nome caso ele não exista. Se você já tiver um aplicativo do Azure Active Directory que deseja usar, insira o nome do aplicativo do Azure Active Directory.

> [!NOTE]
> Se você estiver curioso sobre o motivo de precisar criar um aplicativo do Azure Active Directory, confira a seção *Registrar um aplicativo no Azure Active Directory* do artigo [Introdução ao Cofre da Chave do Azure](../key-vault/key-vault-get-started.md).
>
>

Execute as etapas a seguir para criptografar uma Máquina Virtual do Azure:

1. Se você tiver fechado o ISE do PowerShell, abra uma instância com privilégios elevados do ISE do PowerShell. Siga as instruções mostradas anteriormente neste artigo se o ISE do PowerShell ainda não estiver aberto. Se você tiver fechado o script, abra o **ADEPrereqScript.ps1** clicando em **Arquivo**, em **Abrir** e selecionando o script na pasta **c:\AzureADEScript**. Se você estiver seguindo este artigo desde o início, vá para a próxima etapa.
2. No console do ISE do PowerShell (o painel inferior do ISE do PowerShell), altere o foco para o local do script ao digitar **cd c:\AzureADEScript** e pressione **ENTER**.
3. Defina a política de execução em seu computador para que você possa executar o script. Digite **Set-ExecutionPolicy Unrestricted** no console e pressione ENTER. Se você vir uma caixa de diálogo informando sobre os efeitos da alteração da política de execução, clique em **Sim para tudo** ou em **Sim** (se você vir **Sim para tudo**, selecione essa opção; se não vir **Sim para todos**, clique em **Sim**).
4. Faça logon na sua conta do Azure. No console, digite **Login-AzureRmAccount** e pressione **ENTER**. Será exibida uma caixa de diálogo na qual você vai inserir suas credenciais (verifique se tem direitos para alterar as máquinas virtuais. Caso não tenha direitos, não poderá criptografá-las. Se você não tiver certeza, pergunte ao proprietário da assinatura ou ao administrador). Você deverá ver informações sobre seu **Ambiente**, **Conta**, **TenantId**, **SubscriptionId** e **CurrentStorageAccount**. Copie o **SubscriptionId** no bloco de notas. Você precisará usá-la na etapa 6.
5. Descubra a assinatura à qual sua máquina virtual pertence e sua localização. Entre em [https://portal.azure.com](ttps://portal.azure.com) e faça logon.  No lado esquerdo da página, clique em **Máquinas Virtuais**. Você verá uma lista de suas máquinas virtuais e as assinaturas às quais elas pertencem.

   ![Máquinas Virtuais](./media/security-center-disk-encryption/security-center-disk-encryption-fig3.png)
6. Volte para o ISE do PowerShell. Defina o contexto da assinatura no qual o script será executado. No console, digite **Select-AzureRmSubscription –SubscriptionId <sua_Id_assinatura >** (substitua **<sua_Id_assinatura >** por sua ID de assinatura real) e pressione **ENTER**. Você deverá ver informações sobre seu Ambiente, **Conta**, **TenantId**, **SubscriptionId** e **CurrentStorageAccount**.
7. Agora você está pronto para executar o script. Clique no botão **Executar Script** ou pressione **F5** no teclado.

   ![Execução de script do PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig4.png)
8. O script solicita o **resourceGroupName:** – insira o nome do *Grupo de Recursos* que você deseja usar e pressione **ENTER**. Se você não tiver um, insira um nome que você deseja usar em um novo grupo. Se você já tiver um *Grupo de Recursos* que deseja usar (como aquele em que sua máquina virtual está), insira o nome do Grupo de Recursos existente.
9. O script solicita o **keyVaultName** : - insira o nome do *Cofre da Chave* que você deseja usar e pressione ENTER. Se você não tiver um, insira um nome que você deseja usar em um novo grupo. Se você já tiver um Cofre da Chave que deseja usar, insira o nome do *Cofre da Chave*existente.
10. O script solicita o **local:** – insira o nome do local no qual a VM que você deseja criptografar está localizada e pressione **ENTER** a seguir. Se você não se lembrar do local, volte para a etapa 5.
11. O script solicita o **aadAppName:** – insira o nome do aplicativo do *Azure Active Directory* que você deseja usar e pressione **ENTER**. Se você não tiver um, insira um nome que você deseja usar em um novo grupo. Se você já tiver um *aplicativo do Azure Active Directory* que deseja usar, insira o nome do *aplicativo do Azure Active Directory* existente.
12. Será exibida uma caixa de diálogo de logon. Forneça suas credenciais (sim, você fez logon uma vez, mas agora precisa fazer novamente).
13. O script é executado e, quando for concluído, solicitará que você copie os valores da **aadClientID**, do **aadClientSecret**, da **diskEncryptionKeyVaultUrl** e da **keyVaultResourceId**. Copie cada um desses valores para a área de transferência e cole-os no Bloco de Notas.
14. Volte para o ISE do PowerShell e coloque o cursor do mouse no final da última linha e pressione **ENTER**.

A saída do script deve ser semelhante à tela abaixo:

![Saída do PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig5.png)

## <a name="encrypt-the-azure-virtual-machine"></a>Criptografar a máquina virtual do Azure
Agora você está pronto para criptografar sua máquina virtual. Se sua máquina virtual estiver localizada no mesmo Grupo de Recursos que seu Cofre da Chave, você poderá prosseguir para a seção de etapas de criptografia. No entanto, se sua máquina virtual não estiver no mesmo Grupo de Recursos que o Cofre da Chave, será necessário inserir o seguinte no console do ISE do PowerShell:

**$resourceGroupName = <’GR_da_Máquina_Virtual’>**

Substitua **<GR_da_Máquina_Virtual>** pelo nome do Grupo de Recursos no qual suas máquinas virtuais estão contidas, entre aspas simples. Em seguida, pressione **ENTER**.
Para confirmar se o nome do Grupo de Recursos correto foi inserido, digite o seguinte no console do ISE do PowerShell:

**$resourceGroupName**

Pressione **ENTER**. Você deverá ver o nome do Grupo de Recursos em que suas máquinas virtuais estão localizadas. Por exemplo:

![Saída do PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig6.png)

### <a name="encryption-steps"></a>Etapas de criptografia
Primeiro, você precisa dizer ao PowerShell o nome da máquina virtual que deseja criptografar. No console, digite:

**$vmName = <’nome_da_sua_vm’>**

Substitua **<'nome_da_sua_vm ' >** pelo nome da sua VM (verifique se o nome está entre aspas simples) e pressione **ENTER**.

Para confirmar se o nome correto da VM foi inserido, digite:

**$vmName**

Pressione **ENTER**. Você deverá ver o nome da máquina virtual que deseja criptografar. Por exemplo:

![Saída do PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig7.png)

Há dois métodos para executar o comando de criptografia para criptografar todas as unidades na máquina virtual. O primeiro método é digitar o seguinte comando no console do ISE do PowerShell:

~~~
Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType All
~~~

Depois de digitar esse comando, pressione **ENTER**.

O segundo método é clicar no painel de script (o painel superior do ISE do PowerShell) e rolar até o final do script. Realce o comando listado acima, clique com o botão direito do mouse nele e clique em **Executar Seleção** ou pressione **F8** no teclado.

![ISE do PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig8.png)

Independentemente do método usado, será exibida uma caixa de diálogo informando que a operação levará de 10 a 15 minutos para ser concluída. Clique em **Sim**.

Durante o processo de criptografia, é possível retornar ao Portal do Azure e ver o status da máquina virtual. No lado esquerdo da página, clique em **Máquinas Virtuais** e, na folha **Máquinas Virtuais**, clique no nome da máquina virtual que você está criptografando. Na folha exibida, você observará que o **Status** diz **Atualizando**. Isso demonstra que a criptografia está em andamento.

![Mais detalhes sobre a VM](./media/security-center-disk-encryption/security-center-disk-encryption-fig9.png)

Volte para o ISE do PowerShell. Quando o script for concluído, você verá o que aparece na figura abaixo.

![Saída do PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig10.png)

Para demonstrar que a máquina virtual já está criptografada, retorne ao Portal do Azure e clique em **Máquinas Virtuais** no lado esquerdo da página. Clique no nome da máquina virtual que você criptografou. Na folha **Configurações**, clique em **Discos**.

![Opções de configurações](./media/security-center-disk-encryption/security-center-disk-encryption-fig11.png)

Na folha **Discos**, você verá que a **Criptografia** está **Habilitada**.

![Propriedades de discos](./media/security-center-disk-encryption/security-center-disk-encryption-fig12.png)

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a criptografar uma Máquina Virtual do Azure. Para saber mais sobre a Central de Segurança do Azure, veja o seguinte:

* [Monitoramento de integridade de segurança na Central de Segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure
* [Gerenciando e respondendo a alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) – aprenda a gerenciar e a responder a alertas de segurança
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md) – encontre perguntas frequentes sobre como usar o serviço
* [Blog de segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – encontre postagens no blog sobre conformidade e segurança do Azure
