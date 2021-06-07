---
title: Tutorial para pedido do Azure Data Box | Microsoft Docs
description: Neste tutorial, saiba mais sobre o Azure Data Box, uma solução híbrida que permite importar dados locais para o Azure, e como pedir o Azure Data Box.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: eae8cca0302993c16ea29adddf6e4ee9b5b24be8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770873"
---
# <a name="tutorial-order-azure-data-box"></a>Tutorial: Solicitar o Azure Data Box

O Azure Data Box é uma solução híbrida que permite importar dados do local no Azure de maneira rápida, fácil e confiável. Você transfere seus dados para um dispositivo de armazenamento de 80 TB (capacidade utilizável) fornecido pela Microsoft e, depois, devolve o dispositivo. Após, esses dados são carregados no Azure.

Este tutorial descreve como você pode solicitar um Azure Data Box. Neste tutorial, você aprenderá a:  

> [!div class="checklist"]
>
> * Pré-requisitos para implantar o Data Box
> * Solicitar um Data Box
> * Acompanhar o pedido
> * Cancelar o pedido

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/portal)

Preencha os seguintes pré-requisitos de configuração para o serviço e dispositivo do Data Box antes de implantar o dispositivo:

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Você pode entrar no Azure e executar comandos da CLI do Azure de uma das duas formas a seguir:

* Você pode instalar a CLI e executar comandos da CLI localmente.
* Você pode executar comandos da CLI de dentro do portal do Azure, no Azure Cloud Shell.

Usamos a CLI do Azure por meio do Windows PowerShell para o tutorial, mas você pode escolher qualquer uma das opções.

### <a name="for-azure-cli"></a>Para CLI do Azure

Antes de começar, verifique se:

#### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

* Instalar a [CLI do Azure](/cli/azure/install-azure-cli) versão 2.0.67 ou posterior. Como alternativa, você pode [instalar usando o MSI](https://aka.ms/installazurecliwindows).

**Entrar no Azure**

Abra uma janela Comando do Windows PowerShell e entre no Azure com o comando [az login](/cli/azure/reference-index#az_login):

```azurecli
PS C:\Windows> az login
```

Aqui está a saída de uma conexão bem-sucedida:

```output
You have logged in. Now let us find all the subscriptions to which you have access.
[
   {
      "cloudName": "AzureCloud",
      "homeTenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "isDefault": true,
      "managedByTenants": [],
      "name": "My Subscription",
      "state": "Enabled",
      "tenantId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
      "user": {
          "name": "gusp@contoso.com",
          "type": "user"
      }
   }
]
```

**Instalar a extensão da CLI do Azure Data Box**

Para usar os comandos da CLI do Azure Data Box, instale a extensão. As extensões da CLI do Azure fornecem acesso a comandos experimentais e de pré-lançamento que ainda não foram enviados como parte da CLI principal. Para obter mais informações sobre extensões, confira [Usar extensões com a CLI do Azure](/cli/azure/azure-cli-extensions-overview).

Para instalar a extensão para o Azure Data Box, execute o seguinte comando: `az extension add --name databox`:

```azurecli

    PS C:\Windows> az extension add --name databox
```

Se a extensão for instalada com êxito, você verá a seguinte saída:

```output
    The installed extension 'databox' is experimental and not covered by customer support. Please use with discretion.
    PS C:\Windows>

    # az databox help

    PS C:\Windows> az databox -h

    Group
        az databox

    Subgroups:
        job [Experimental] : Commands to manage databox job.

    For more specific examples, use: az find "az databox"

        Please let us know how we are doing: https://aka.ms/clihats
```

#### <a name="use-azure-cloud-shell"></a>Usar o Azure Cloud Shell

Você pode usar [Azure Cloud Shell](https://shell.azure.com/), um ambiente de shell interativo hospedado do Azure, por meio de seu navegador para executar comandos da CLI. O Azure Cloud Shell dá suporte a Bash ou Windows PowerShell com serviços do Azure. A CLI do Azure é pré-instalada e configurada para usar com sua conta. Selecione o botão Cloud Shell no menu na seção superior direita do portal do Azure:

![Seleção de menu do Cloud Shell](../storage/common/media/storage-quickstart-create-account/cloud-shell-menu.png)

O botão inicia um shell interativo que você pode usar para executar as etapas descritas neste artigo de instruções.

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

[!INCLUDE [Prerequisites](../../includes/data-box-deploy-ordered-prerequisites.md)]

### <a name="for-azure-powershell"></a>Para o Azure PowerShell

Antes de começar:

* Instale o Windows PowerShell 6.2.4 ou posterior.
* Instale o módulo do Azure PowerShell (AZ).
* Instale o módulo do Azure Data Box (Az.DataBox).
* Entre no Azure.

#### <a name="install-azure-powershell-and-modules-locally"></a>Instalar o Azure PowerShell e os módulos localmente

**Instalar ou atualizar o Windows PowerShell**

Você precisará ter o Windows PowerShell versão 6.2.4 ou posterior instalado. Para saber qual versão do PowerShell você instalou, execute: `$PSVersionTable`.

Você verá esta saída:

```azurepowershell
    PS C:\users\gusp> $PSVersionTable
    
    Name                           Value
    ----                           -----
    PSVersion                      6.2.3
    PSEdition                      Core
    GitCommitId                    6.2.3
    OS                             Microsoft Windows 10.0.18363
    Platform                       Win32NT
    PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0…}
    PSRemotingProtocolVersion      2.3
    SerializationVersion           1.1.0.1
    WSManStackVersion              3.0
```

Caso sua versão do Windows PowerShell seja inferior à 6.2.4, você precisará atualizá-la. Para instalar a última versão do PowerShell, confira [Instalar o Azure PowerShell](/powershell/scripting/install/installing-powershell).

**Instalar os módulos do Azure PowerShell e Data Box**

Você precisará instalar os módulos do Azure PowerShell a fim de usar o Azure PowerShell para solicitar um Azure Data Box. Para instalar os módulos do Azure PowerShell:

1. Instale o [módulo Az do Azure PowerShell](/powershell/azure/new-azureps-module-az).
2. Instale o Az.DataBox usando o comando `Install-Module -Name Az.DataBox`.

```azurepowershell
PS C:\PowerShell\Modules> Install-Module -Name Az.DataBox
PS C:\PowerShell\Modules> Get-InstalledModule -Name "Az.DataBox"

Version              Name                                Repository           Description
-------              ----                                ----------           -----------
0.1.1                Az.DataBox                          PSGallery            Microsoft Azure PowerShell - DataBox ser…
```

#### <a name="sign-in-to-azure"></a>Entrar no Azure

Abra uma janela de comando do Windows PowerShell e entre no Azure com o comando [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount):

```azurepowershell
PS C:\Windows> Connect-AzAccount
```

Aqui está a saída de uma conexão bem-sucedida:

```output
WARNING: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FSBFZMBKC to authenticate.

Account              SubscriptionName                          TenantId                             Environment
-------              ----------------                          --------                             -----------
gusp@contoso.com     MySubscription                            aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa AzureCloud

PS C:\Windows\System32>
```

Para obter informações detalhadas sobre como entrar no Azure usando o Windows PowerShell, confira [Entrar com o Azure PowerShell](/powershell/azure/authenticate-azureps).

---

## <a name="order-data-box"></a>Solicitar Data Box

# <a name="portal"></a>[Portal](#tab/portal)

Execute as etapas a seguir no portal do Azure para solicitar um dispositivo.

1. Use suas credenciais do Microsoft Azure para entrar nesta URL: [https://portal.azure.com](https://portal.azure.com).
2. Selecione **+ Criar um recurso** e pesquise *Azure Data Box*. Selecione **Azure Data Box**.

   ![Uma captura de tela da seção Novo com o Azure Data Box no campo de pesquisa](media/data-box-deploy-ordered/select-data-box-import-02.png)

3. Selecione **Criar**.

   ![Uma captura de tela da seção Azure Data Box com a opção Criar destacada](media/data-box-deploy-ordered/select-data-box-import-03.png)

4. Verifique se o serviço Data Box está disponível em sua região. Insira ou selecione as informações a seguir e selecione **Aplicar**.

    |Configuração  |Valor  |
    |---------|---------|
    |Tipo de transferência     | Selecione **Importar no Azure**.        |
    |Subscription     | Selecione uma assinatura do EA, do CSP ou do Azure Sponsorship para o serviço Data Box. <br> A assinatura está vinculada à sua conta de cobrança.       |
    |Grupo de recursos | Selecione um grupo de recursos existente. Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. |
    |País/região de origem    |    Selecione o país/região em que os dados residem no momento.         |
    |Região do Azure de destino     |     Selecione a região do Azure para onde você deseja transferir dados. <br> Para saber mais, acesse [disponibilidade por região](data-box-overview.md#region-availability).            |

    [ ![Como iniciar um pedido de importação do Azure Data Box](media/data-box-deploy-ordered/select-data-box-import-04-b.png) ](media/data-box-deploy-ordered/select-data-box-import-04-b.png#lightbox)

5. Selecione **Data Box**. A capacidade máxima utilizável de um único pedido é de 80 TB. É possível criar vários pedidos para tamanhos maiores de dados.

    ![Tamanhos disponíveis de dados: Data Box Disk: 40 terabytes, Data Box: 100 terabytes, Data Box Heavy: 1.000 terabytes. Enviar discos próprios: 1 terabyte](media/data-box-deploy-ordered/select-data-box-import-05.png)

6. Em **Pedido**, vá para a guia **Básico**. Insira ou selecione as seguintes informações e selecione **Avançar: Destino dos dados>** .

    |Configuração  |Valor  |
    |---------|---------|
    |Subscription      | A assinatura é preenchida automaticamente com base na seleção anterior.|
    |Resource group    | O grupo de recursos selecionado anteriormente. |
    |Nome do pedido de importação | Forneça um nome amigável para acompanhar o pedido. <br> O nome pode ter entre 3 e 24 caracteres que podem ser letras, números e hífens. <br> O nome deve começar e terminar com uma letra ou um número.    |

    ![Assistente para Pedidos de importação do Data Box – Tela básica com as informações corretas preenchidas](media/data-box-deploy-ordered/select-data-box-import-06.png)

7. Na tela **Destino de dados**, selecione o **Destino de dados** – contas de armazenamento ou discos gerenciados.

    Se você usar as **contas de armazenamento** como o destino de armazenamento, verá a seguinte tela:

    ![Assistente para Pedidos de importação do Data Box – Tela de destino de dados com as contas de armazenamento selecionadas](media/data-box-deploy-ordered/select-data-box-import-07.png)

    Com base na região especificada do Azure, selecione uma ou mais contas de armazenamento na lista filtrada de contas de armazenamento existentes. O Data Box pode ser vinculada a até 10 contas de armazenamento. Você também pode criar uma nova conta de **Uso geral v1**, **Uso geral v2** ou de **Armazenamento de Blobs**.

   > [!NOTE]
   > - Se você selecionar contas do Azure Premium FileStorage, a cota provisionada no compartilhamento da conta de armazenamento aumentará o tamanho dos dados que estão sendo copiados para os compartilhamentos de arquivo. Depois que a cota for aumentada, ela não será ajustada novamente, por exemplo, se por algum motivo o Data Box não puder copiar seus dados.
   > - Essa cota é usada para cobrança. Depois que os dados são carregados no datacenter, você deve ajustar a cota de acordo com as suas necessidades. Para obter mais informações, confira [Noções básicas sobre cobrança](../../articles/storage/files/understanding-billing.md).

    Contas de armazenamento com redes virtuais são compatíveis. Para permitir que o serviço do Data Box trabalhe com contas de armazenamento protegido, habilite os serviços confiáveis em definições de firewall de rede da conta de armazenamento. Para obter mais informações, confira como [Adicionar o Azure Data Box como um serviço confiável](../storage/common/storage-network-security.md#exceptions).

    Se você usar o Data Box para criar **Discos gerenciados** dos VHDs (discos rígidos virtuais) locais, também precisará fornecer as seguintes informações:

    |Configuração  |Valor  |
    |---------|---------|
    |Grupos de recursos     | Se você pretende criar discos gerenciados de VHDs locais, crie novos grupos de recursos. Você poderá usar um grupo de recursos existente somente se o grupo de recursos tiver sido criado anteriormente durante a criação de um pedido do Data Box para discos gerenciados pelo serviço Data Box. <br> Especifique vários grupos de recursos separados por ponto e vírgula. Há suporte para um limite máximo de dez grupos de recursos.|

    ![Assistente para Pedidos de importação do Data Box – Tela de destino de dados com a opção Managed Disks selecionada](media/data-box-deploy-ordered/select-data-box-import-07-b.png)

    A conta de armazenamento especificada para os discos gerenciados é usada como uma conta de armazenamento de preparo. O serviço do Data Box carrega os VHDs como blobs de páginas na conta de armazenamento de preparo antes de convertê-los em discos gerenciados e movê-los para os grupos de recursos. Para obter mais informações, confira [Verificar o upload de dados para o Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

   > [!NOTE]
   > Caso um blob de páginas não seja convertido com êxito em um disco gerenciado, ele permanecerá na conta de armazenamento e você será cobrado pelo armazenamento.

8. Selecione **Avançar: segurança** para continuar.

    A tela **Segurança** permite que você use a própria chave de criptografia e o próprio dispositivo, compartilhe senhas e opte por usar a criptografia dupla.

    Todas as configurações na tela **Segurança** são opcionais. Se você não alterar nenhuma configuração, as configurações padrão serão aplicadas.

    ![Tela de segurança do assistente para Pedidos de importação do Data Box](media/data-box-deploy-ordered/select-data-box-import-security-01.png)

9. Se você quiser usar a própria chave gerenciada pelo cliente para proteger a chave de acesso de desbloqueio para o novo recurso, expanda **Tipo de criptografia**.

    A configuração de uma chave gerenciada pelo cliente para seu Azure Data Box é opcional. Por padrão, o Data Box usa uma chave gerenciada da Microsoft para proteger a chave de acesso de desbloqueio.

    Uma chave gerenciada pelo cliente não afeta como os dados no dispositivo são criptografados. A chave é usada somente para criptografar a chave de acesso de desbloqueio do dispositivo.

    Se você não quiser usar uma chave gerenciada pelo cliente, pule para a Etapa 15.

   ![Tela de segurança mostrando as configurações Tipo de criptografia](./media/data-box-deploy-ordered/customer-managed-key-01.png)

10. Selecione **Chave gerenciada pelo cliente** como o tipo de chave. Em seguida, selecione **Selecionar um cofre de chaves e uma chave**.
   
    ![Tela de segurança, configurações para uma chave gerenciada pelo cliente](./media/data-box-deploy-ordered/customer-managed-key-02.png)

11. Na folha **Selecionar chave do Azure Key Vault**, a assinatura é preenchida automaticamente.

    - Para **Cofre de chaves**, você pode selecionar um cofre de chaves existente na lista suspensa.

      ![Tela Selecionar chave do Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-03.png)

    - Você também pode selecionar **Criar** para criar um cofre de chaves. Na tela **Criar cofre de chaves**, insira o grupo de recursos e um nome do cofre de chaves. Verifique se as opções **Exclusão temporária** e **Proteção contra limpeza** estão habilitadas. Aceite todos os outros padrões e selecione **Examinar + Criar**.

      ![Configurações para Criar um Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-04.png)

      Examine as informações do seu cofre de chaves e selecione **Criar**. Aguarde alguns minutos até que a criação do cofre de chaves seja concluída.

      ![Tela de revisão Novo Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-05.png)

12. Em **Selecionar chave do Azure Key Vault**, você pode selecionar uma chave existente no cofre de chaves.

    ![Selecionar chave existente do Azure Key Vault](./media/data-box-deploy-ordered/customer-managed-key-06.png)

    Se você desejar criar uma chave, selecione **Criar**. Você precisa usar uma chave RSA. O tamanho pode ser 2048 ou maior. Insira um nome para a nova chave, aceite os outros padrões e selecione **Criar**.

      ![Opção Criar uma chave](./media/data-box-deploy-ordered/customer-managed-key-07.png)

      Você será notificado quando a chave tiver sido criada no cofre de chaves.

13. Selecione a **Versão** da chave a ser usada e, em seguida, escolha **Selecionar**.

      ![Chave criada no cofre de chaves](./media/data-box-deploy-ordered/customer-managed-key-08.png)

    Se você desejar criar uma versão da chave, selecione **Criar**.

    ![Abrir uma caixa de diálogo para criar uma versão de chave](./media/data-box-deploy-ordered/customer-managed-key-08-a.png)

    Escolha configurações para a nova versão de chave e selecione **Criar**.

    ![Criar uma nova versão da chave](./media/data-box-deploy-ordered/customer-managed-key-08-b.png)

    As configurações **Tipo de criptografia** na tela **Segurança** mostram seu cofre de chaves e sua chave.

    ![Chave e cofre de chaves para uma chave gerenciada pelo cliente](./media/data-box-deploy-ordered/customer-managed-key-09.png)

14. Selecione uma identidade de usuário que você usará para gerenciar o acesso a esse recurso. Escolha **Selecionar uma identidade de usuário**. No painel à direita, selecione a assinatura e a identidade gerenciada a ser usada. Em seguida, escolha **Selecionar**.

    Uma identidade gerenciada atribuída pelo usuário é um recurso do Azure autônomo que pode ser usado para gerenciar vários recursos. Para obter mais informações, confira [Tipos de identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md).  

    Se você precisar criar uma identidade gerenciada, siga as diretrizes em [Criar, listar, excluir ou atribuir uma função a uma identidade gerenciada atribuída pelo usuário usando o portal do Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
    
    ![Selecionar uma identidade de usuário](./media/data-box-deploy-ordered/customer-managed-key-10.png)

    A identidade de usuário é mostrada nas configurações **Tipo de criptografia**.

    ![Uma identidade de usuário selecionada mostrada nas configurações Tipo de criptografia](./media/data-box-deploy-ordered/customer-managed-key-11.png)

15. Se você não quiser usar as senhas geradas pelo sistema que o Azure Data Box usa por padrão, expanda **Traga a própria senha** na tela **Segurança**.

    As senhas geradas pelo sistema são seguras e recomendadas, a menos que sua organização exija o contrário.

    ![Opções de Traga a própria senha expandidas para um pedido de importação do Data Box](media/data-box-deploy-ordered/select-data-box-import-security-02.png) 

   - Para usar a própria senha para o novo dispositivo, em **Definir preferência para a senha do dispositivo**, selecione **Usar a própria senha** e digite uma senha que atenda aos requisitos de segurança.
     
     A senha deverá ser alfanumérica e conter de 12 a 15 caracteres com pelo menos uma letra maiúscula, uma letra minúscula, um caractere especial e um número. 

     - Os caracteres especiais permitidos são: @ # - $ % ^ ! + = ; : _ ( )
     - Estes caracteres não são permitidos: i L o O 0
   
     ![Opções para usar a própria senha do dispositivo na tela de Segurança para um pedido de importação do Data Box](media/data-box-deploy-ordered/select-data-box-import-security-03.png)

 - Para usar as próprias senhas para compartilhamentos:

   1. Em **Definir preferência para senhas de compartilhamento**, selecione **Usar as próprias senhas** e **Selecionar senhas para os compartilhamentos**.
     
       ![Opções para usar as próprias senhas de compartilhamento na tela de Segurança para pedidos de importação do Data Box](media/data-box-deploy-ordered/select-data-box-import-security-04.png)

    1. Digite uma senha para cada conta de armazenamento no pedido. A senha será usada em todos os compartilhamentos para a conta de armazenamento.
    
       A senha deverá ser alfanumérica e conter de 12 a 64 caracteres com pelo menos uma letra maiúscula, uma letra minúscula, um caractere especial e um número.

       - Os caracteres especiais permitidos são: @ # - $ % ^ ! + = ; : _ ( )
       - Estes caracteres não são permitidos: i L o O 0
     
    1. Para usar a mesma senha para todas as contas de armazenamento, selecione **Copiar para todos**. 

    1. Quando terminar, selecione **Salvar**.
     
       ![Tela para inserir senhas de compartilhamento para um pedido de importação do Data Box](media/data-box-deploy-ordered/select-data-box-import-security-05.png)

    Na tela de **Segurança**, será possível usar a opção **Exibir ou alterar senhas** para alterar as senhas.

16. Em **Segurança**, caso queira habilitar uma criptografia dupla baseada em software, expanda **Criptografia dupla (para ambientes altamente seguros)** e selecione **Habilitar criptografia dupla para o pedido**.

    ![Tela Segurança para a importação do Data Box, habilitando a criptografia baseada em software para um pedido do Data Box](media/data-box-deploy-ordered/select-data-box-import-security-07.png)

    A criptografia baseada em software será executada além da criptografia AES de 256 bits dos dados no Data Box.

    > [!NOTE]
    > Habilitar essa opção poderá fazer com que o processamento de pedidos e a cópia de dados demorem mais. Não será possível alterar essa opção depois de criar seu pedido.

    Selecione **Avançar: Detalhes de contato** para continuar.

17. Em **Detalhes de contato**, selecione **+ Adicionar Endereço para Entrega**.

    ![Na tela Detalhes de Contato, adicione ao pedido de importação do Azure Data Box os endereços para entrega](media/data-box-deploy-ordered/select-data-box-import-08-a.png)

18. No **Endereço para remessa**, forneça seu nome e sobrenome, nome e endereço da empresa e um número de telefone válido. Selecione **Validar endereço**. O serviço valida o endereço de remessa para a disponibilidade do serviço. Se o serviço está disponível para o endereço de remessa especificado, você receberá uma notificação para esse efeito.

    ![Captura de tela da caixa de diálogo Adicionar Endereço para Entrega com as opções de Enviar usando e a opção Adicionar endereço para entrega em destaque.](media/data-box-deploy-ordered/select-data-box-import-10.png)

    Se você tiver selecionado remessa autogerenciada, receberá uma notificação por email depois que o pedido for feito com sucesso. Para saber mais sobre a remessa autogerenciada, confira [Usar a remessa autogerenciada](data-box-portal-customer-managed-shipping.md).

19. Selecione **Adicionar Endereço para Entrega** após os detalhes do envio serem validados com sucesso. Você retornará para a guia **Detalhes de contato**.

20. Após retornar para **Detalhes de contato**, adicione um ou mais endereços de email. O serviço envia as notificações por email em relação a quaisquer atualizações do status do pedido para os endereços de email especificados.

    É recomendável usar um email de grupo para que você continue a receber notificações caso um administrador do grupo saia.

    ![Seção de email dos Detalhes de Contato no assistente para Pedidos](media/data-box-deploy-ordered/select-data-box-import-08-c.png)

21. Examine as informações em **Examinar + Solicitar** relacionadas com o pedido, o contato, a notificação e os termos de privacidade. Marque a caixa correspondente ao contrato de termos de privacidade.

22. Selecione **Pedido**. A criação do pedido demora alguns minutos.

    ![Tela de Análise e Pedidos do assistente para Pedidos](media/data-box-deploy-ordered/select-data-box-import-11.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Execute as seguintes etapas usando a CLI do Azure para solicitar um dispositivo:

1. Anote suas configurações para seu pedido do Data Box. Essas configurações incluem suas informações pessoais/empresariais, nome da assinatura, informações do dispositivo e informações de envio. Você precisará usar essas configurações como parâmetros ao executar o comando da CLI para criar o pedido do Data Box. A seguinte tabela mostra as configurações de parâmetro usadas para `az databox job create`:

   | Configuração (parâmetro) | Descrição |  Valor de exemplo |
   |---|---|---|
   |resource-group| Use um grupo existente ou crie um novo. Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. | "myresourcegroup"|
   |name| O nome do pedido que você está criando. | "mydataboxorder"|
   |contact-name| O nome associado ao endereço para entrega. | "Gus Poland"|
   |phone| O número de telefone da pessoa ou da empresa que receberá o pedido.| "14255551234"
   |local| A região do Azure mais próxima a você que enviará seu dispositivo.| "Oeste dos EUA"|
   |sku| O dispositivo Data Box específico que você está solicitando. Os valores válidos são: "DataBox", "DataBoxDisk" e "DataBoxHeavy"| "DataBox" |
   |email-list| Os endereços de email associados ao pedido.| "gusp@contoso.com" |
   |street-address1| O endereço para envio do pedido. | "15700 NE 39th St" |
   |street-address2| As informações de endereço secundário, como número do apartamento ou número do edifício. | "Prédio 123" |
   |city| A cidade para a qual o dispositivo será enviado. | "Redmond" |
   |state-or-province| O Estado para o qual o dispositivo será enviado.| "WA" |
   |country| O país para o qual o dispositivo será enviado. | "Estados Unidos" |
   |postal-code| O CEP ou o código postal associado ao endereço para entrega.| "98052"|
   |company-name| O nome da empresa para a qual você trabalha.| "Contoso, LTD" |
   |do Azure| A conta de Armazenamento do Azure da qual você deseja importar dados.| "mystorageaccount"|
   |depurar| Incluir informações de depuração para registro em log detalhado  | --debug |
   |ajuda| Exibir informações de ajuda para esse comando. | --help -h |
   |only-show-errors| Mostrar apenas erros, suprimindo avisos. | --only-show-errors |
   |output -o| Define o formato de saída.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc. O valor padrão é json. | --output "json" |
   |Consulta| A cadeia de caracteres de consulta JMESPath. Para obter mais informações, confira [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Incluir o registro em log detalhado. | --verbose |

2. No prompt de comando ou terminal escolhido, execute [az data box job create](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-create) para criar seu pedido do Azure Data Box.

   ```azurecli
   az databox job create --resource-group <resource-group> --name <order-name> --location <azure-location> --sku <databox-device-type> --contact-name <contact-name> --phone <phone-number> --email-list <email-list> --street-address1 <street-address-1> --street-address2 <street-address-2> --city "contact-city" --state-or-province <state-province> --country <country> --postal-code <postal-code> --company-name <company-name> --storage-account "storage-account"
   ```

   Aqui está um exemplo de uso do comando:

   ```azurecli
   az databox job create --resource-group "myresourcegroup" \
                         --name "mydataboxtest3" \
                         --location "westus" \
                         --sku "DataBox" \
                         --contact-name "Gus Poland" \
                         --phone "14255551234" \
                         --email-list "gusp@contoso.com" \
                         --street-address1 "15700 NE 39th St" \
                         --street-address2 "Bld 25" \
                         --city "Redmond" \
                         --state-or-province "WA" \
                         --country "US" \
                         --postal-code "98052" \
                         --company-name "Contoso" \
                         --storage-account mystorageaccount
   ```

   Aqui está a saída da execução do comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   {
     "cancellationReason": null,
     "deliveryInfo": {
        "scheduledDateTime": "0001-01-01T00:00:00+00:00"
   },
   "deliveryType": "NonScheduled",
   "details": null,
   "error": null,
   "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.DataBox/jobs/mydataboxtest3",
   "identity": {
     "type": "None"
   },
   "isCancellable": true,
   "isCancellableWithoutFee": true,
   "isDeletable": false,
   "isShippingAddressEditable": true,
   "location": "westus",
   "name": "mydataboxtest3",
   "resourceGroup": "myresourcegroup",
   "sku": {
     "displayName": null,
     "family": null,
     "name": "DataBox"
   },
   "startTime": "2020-06-10T23:28:27.354241+00:00",
   "status": "DeviceOrdered",
   "tags": {},
   "type": "Microsoft.DataBox/jobs"

   }
   PS C:\Windows>

   ```

3. Todos os comandos da CLI do Azure usarão json como o formato de saída por padrão, a menos que você o altere. Você pode alterar o formato de saída usando o parâmetro global `--output <output-format>`. Alterar o formato para "table" aprimorará a legibilidade da saída.

   Aqui está o mesmo comando que acabamos de executar com um pequeno ajuste para alterar a formatação:

    ```azurecli
    az databox job create --resource-group "myresourcegroup" --name "mydataboxtest4" --location "westus" --sku "DataBox" --contact-name "Gus Poland" --phone "14255551234" --email-list "gusp@contoso.com" --street-address1 "15700 NE 39th St" --street-address2 "Bld 25" --city "Redmond" --state-or-province "WA" --country "US" --postal-code "98052" --company-name "Contoso" --storage-account mystorageaccount --output "table"
   ```

   Aqui está a saída da execução do comando:

   ```output

    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered

    ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

Realize as seguintes etapas usando o Azure PowerShell para solicitar um dispositivo:

1. Antes de criar o pedido de importação, você precisará obter sua conta de armazenamento e salvar o objeto da conta de armazenamento em uma variável.

   ```azurepowershell
    $storAcct = Get-AzStorageAccount -Name "mystorageaccount" -ResourceGroup "myresourcegroup"
   ```

2. Anote suas configurações para seu pedido do Data Box. Essas configurações incluem suas informações pessoais/empresariais, nome da assinatura, informações do dispositivo e informações de envio. Você precisará usar essas configurações como parâmetros ao executar o comando do PowerShell para criar o pedido do Data Box. A tabela a seguir mostra as configurações de parâmetro usadas para [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob).

    | Configuração (parâmetro) | Descrição |  Valor de exemplo |
    |---|---|---|
    |ResourceGroupName [obrigatório]| Usar um grupo de recursos existente. Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. | "myresourcegroup"|
    |Name [obrigatório]| O nome do pedido que você está criando. | "mydataboxorder"|
    |ContactName [obrigatório]| O nome associado ao endereço para entrega. | "Gus Poland"|
    |PhoneNumber [obrigatório]| O número de telefone da pessoa ou da empresa que receberá o pedido.| "14255551234"
    |Location [obrigatório]| A região do Azure mais próxima a você que enviará seu dispositivo.| "WestUS"|
    |DataBoxType [obrigatório]| O dispositivo Data Box específico que você está solicitando. Os valores válidos são: "DataBox", "DataBoxDisk" e "DataBoxHeavy"| "DataBox" |
    |EmailId [obrigatório]| Os endereços de email associados ao pedido.| "gusp@contoso.com" |
    |StreetAddress1 [obrigatório]| O endereço para envio do pedido. | "15700 NE 39th St" |
    |StreetAddress2| As informações de endereço secundário, como número do apartamento ou número do edifício. | "Prédio 123" |
    |StreetAddress3| As informações de endereço terciário. | |
    |City [obrigatório]| A cidade para a qual o dispositivo será enviado. | "Redmond" |
    |StateOrProvinceCode [obrigatório]| O Estado para o qual o dispositivo será enviado.| "WA" |
    |CountryCode [obrigatório]| O país para o qual o dispositivo será enviado. | "Estados Unidos" |
    |PostalCode [obrigatório]| O CEP ou o código postal associado ao endereço para entrega.| "98052"|
    |CompanyName| O nome da empresa para a qual você trabalha.| "Contoso, LTD" |
    |StorageAccountResourceId [obrigatório]| A ID da conta de Armazenamento do Azure da qual você deseja importar dados.| <AzStorageAccount>.id |

3. No seu prompt de comando ou terminal preferido, use [New-AzDataBoxJob](/powershell/module/az.databox/New-AzDataBoxJob) para criar seu pedido do Azure Data Box.

   ```azurepowershell
    PS> $storAcct = Get-AzureStorageAccount -StorageAccountName "mystorageaccount"
    PS> New-AzDataBoxJob -Location "WestUS" \
                         -StreetAddress1 "15700 NE 39th St" \
                         -PostalCode "98052" \
                         -City "Redmond" \
                         -StateOrProvinceCode "WA" \
                         -CountryCode "US" \
                         -EmailId "gusp@contoso.com" \
                         -PhoneNumber 4255551234 \
                         -ContactName "Gus Poland" \
                         -StorageAccount $storAcct.id \
                         -DataBoxType DataBox \
                         -ResourceGroupName "myresourcegroup" \
                         -Name "myDataBoxOrderPSTest"
   ```

   Aqui está a saída da execução do comando:

   ```output
    jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
    ----------------     -------------------- ------------------ --------------------- -------------------- -------------
    myDataBoxOrderPSTest DataBox              DeviceOrdered      07-06-2020 05:25:30   westus               myresourcegroup
   ```

---

## <a name="track-the-order"></a>Acompanhar o pedido

# <a name="portal"></a>[Portal](#tab/portal)

Depois de fazer o pedido, você pode acompanhar o status do pedido no portal do Azure. Acesse seu pedido do Data Box e, em seguida, acesse **Visão Geral** para exibir o status. O portal mostra o pedido sob o estado **Pedido**.

Se o dispositivo não estiver disponível, você receberá uma notificação. Se o dispositivo estiver disponível, a Microsoft identificará o dispositivo para envio e o preparará. Durante a preparação do dispositivo, ocorrerão as seguintes ações:

* Os compartilhamentos SMB são criados para cada conta de armazenamento associada ao dispositivo.
* Para cada compartilhamento, as credenciais de acesso, como nome de usuário e senha, são geradas.
* Também é gerada uma senha do dispositivo que ajuda a desbloqueá-lo.
* O Data Box é bloqueado para impedir o acesso não autorizado ao dispositivo.

Após a conclusão da preparação do dispositivo, o portal mostra o pedido no estado **Processado**.

![Um pedido do Data Box que foi processado](media/data-box-overview/data-box-order-status-processed.png)

Em seguida, a Microsoft preparará e enviará seu dispositivo por meio de uma operadora regional. Após o envio do dispositivo, você receberá um número de acompanhamento. O portal mostra o pedido no estado **Despachado**.

![Um pedido do Data Box que foi despachado](media/data-box-overview/data-box-order-status-dispatched.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="track-a-single-order"></a>Acompanhar um pedido

Para obter informações de acompanhamento sobre um só pedido do Azure Data Box existente, execute [`az databox job show`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-show). O comando exibe informações sobre o pedido como, entre outras: nome, grupo de recursos, informações de acompanhamento, ID da assinatura, informações de contato, tipo de remessa e SKU do dispositivo.

   ```azurecli
   az databox job show --resource-group <resource-group> --name <order-name>
   ```

   A seguinte tabela mostra as informações de parâmetro para `az databox job show`:

   | Parâmetro | Descrição |  Valor de exemplo |
   |---|---|---|
   |resource-group [Obrigatório]| O nome do grupo de recursos associado ao pedido. Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. | "myresourcegroup"|
   |name [Obrigatório]| O nome do pedido a ser exibido. | "mydataboxorder"|
   |depurar| Incluir informações de depuração para registro em log detalhado | --debug |
   |ajuda| Exibir informações de ajuda para esse comando. | --help -h |
   |only-show-errors| Mostrar apenas erros, suprimindo avisos. | --only-show-errors |
   |output -o| Define o formato de saída.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc. O valor padrão é json. | --output "json" |
   |Consulta| A cadeia de caracteres de consulta JMESPath. Para obter mais informações, confira [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Incluir o registro em log detalhado. | --verbose |

   Aqui está um exemplo do comando com o formato de saída definido como "table":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job show --resource-group "myresourcegroup" \
                                                --name "mydataboxtest4" \
                                                --output "table"
   ```

   Aqui está a saída da execução do comando:

   ```output
    Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
    DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name            ResourceGroup    StartTime                         Status
    --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  --------------  ---------------  --------------------------------  -------------
    NonScheduled    True             True                       False          True                         westus      mydataboxtest4  myresourcegroup  2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   ```

> [!NOTE]
> Pode haver suporte para o pedido de lista no nível da assinatura, o que torna o grupo de recursos um parâmetro opcional (em vez de um parâmetro obrigatório).

### <a name="list-all-orders"></a>Listar todos os pedidos

Se você tiver pedido vários dispositivos, execute [`az databox job list`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-list) para ver todos os pedidos do Azure Data Box. O comando lista todos os pedidos que pertencem a um grupo de recursos específico. Também exibido na saída: nome do pedido, status da remessa, região do Azure, tipo de entrega, status do pedido. Pedidos cancelados também são incluídos na lista.
O comando também exibe carimbos de data/hora de cada pedido.

```azurecli
az databox job list --resource-group <resource-group>
```

A seguinte tabela mostra as informações de parâmetro para `az databox job list`:

   | Parâmetro | Descrição |  Valor de exemplo |
   |---|---|---|
   |resource-group [Obrigatório]| O nome do grupo de recursos que contém os pedidos. Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. | "myresourcegroup"|
   |depurar| Incluir informações de depuração para registro em log detalhado | --debug |
   |ajuda| Exibir informações de ajuda para esse comando. | --help -h |
   |only-show-errors| Mostrar apenas erros, suprimindo avisos. | --only-show-errors |
   |output -o| Define o formato de saída.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc. O valor padrão é json. | --output "json" |
   |Consulta| A cadeia de caracteres de consulta JMESPath. Para obter mais informações, confira [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Incluir o registro em log detalhado. | --verbose |

   Aqui está um exemplo do comando com o formato de saída definido como "table":

   ```azurecli
    PS C:\WINDOWS\system32> az databox job list --resource-group "GDPTest" --output "table"
   ```

   Aqui está a saída da execução do comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   CancellationReason                                               DeliveryType    IsCancellable    IsCancellableWithoutFee    IsDeletable    IsShippingAddressEditable    Location    Name                 ResourceGroup    StartTime                         Status
   ---------------------- ----------------------------------------  --------------  ---------------  -------------------------  -------------  ---------------------------  ----------  -------------------  ---------------  --------------------------------  -------------
   OtherReason This was a test order for documentation purposes.    NonScheduled    False            False                      True           False                        westus      gdpImportTest        MyResGrp         2020-05-26T23:20:57.464075+00:00  Cancelled
   NoLongerNeeded This order was created for documentation purposes.NonScheduled    False            False                      True           False                        westus      mydataboxExportTest  MyResGrp         2020-05-27T00:04:16.640397+00:00  Cancelled
   IncorrectOrder                                                   NonScheduled    False            False                      True           False                        westus      mydataboxtest2       MyResGrp         2020-06-10T16:54:23.509181+00:00  Cancelled
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest3       MyResGrp         2020-06-11T22:05:49.436622+00:00  DeviceOrdered
                                                                    NonScheduled    True             True                       False          True                         westus      mydataboxtest4       MyResGrp         2020-06-18T03:48:00.905893+00:00  DeviceOrdered
   PS C:\WINDOWS\system32>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="track-a-single-order"></a>Acompanhar um pedido

Para obter informações de acompanhamento sobre um único pedido do Azure Data Box existente, execute [Get-AzDataBoxJob](/powershell/module/az.databox/Get-AzDataBoxJob). O comando exibe informações sobre o pedido como, entre outras: nome, grupo de recursos, informações de acompanhamento, ID da assinatura, informações de contato, tipo de remessa e SKU do dispositivo.

> [!NOTE]
> `Get-AzDataBoxJob` é usado para exibir um único pedido ou vários. A diferença é que você especifica o nome do pedido para pedidos únicos.

   ```azurepowershell
    Get-AzDataBoxJob -ResourceGroupName <String> -Name <String>
   ```

   A seguinte tabela mostra as informações de parâmetro para `Get-AzDataBoxJob`:

   | Parâmetro | Descrição |  Valor de exemplo |
   |---|---|---|
   |ResourceGroup [obrigatório]| O nome do grupo de recursos associado ao pedido. Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. | "myresourcegroup"|
   |Name [obrigatório]| O nome do pedido do qual você deseja obter informações. | "mydataboxorder"|
   |ResourceId| A ID do recurso associado ao pedido. |  |

   Aqui está um exemplo do comando com a saída:

   ```azurepowershell
    PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup" -Name "myDataBoxOrderPSTest"
   ```

   Aqui está a saída da execução do comando:

   ```output
   jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
   ----------------     -------------------- ------------------ --------------------- -------------------- -------------
   myDataBoxOrderPSTest DataBox              DeviceOrdered      7/7/2020 12:37:16 AM  WestUS               myResourceGroup
   ```

### <a name="list-all-orders"></a>Listar todos os pedidos

Se você tiver pedido vários dispositivos, execute [`Get-AzDataBoxJob`](/powershell/module/az.databox/Get-AzDataBoxJob) para ver todos os pedidos do Azure Data Box. O comando lista todos os pedidos que pertencem a um grupo de recursos específico. Também exibido na saída: nome do pedido, status da remessa, região do Azure, tipo de entrega, status do pedido. Pedidos cancelados também são incluídos na lista.
O comando também exibe carimbos de data/hora de cada pedido.

```azurepowershell
Get-AzDataBoxJob -ResourceGroupName <String>
```

Aqui está um exemplo do comando:

```azurepowershell
PS C:\WINDOWS\system32> Get-AzDataBoxJob -ResourceGroupName "myResourceGroup"
```

Aqui está a saída da execução do comando:

```output
jobResource.Name     jobResource.Sku.Name jobResource.Status jobResource.StartTime jobResource.Location ResourceGroup
----------------     -------------------- ------------------ --------------------- -------------------- -------------
guspImportTest       DataBox              Cancelled          5/26/2020 11:20:57 PM WestUS               myResourceGroup
mydataboxExportTest  DataBox              Cancelled          5/27/2020 12:04:16 AM WestUS               myResourceGroup
mydataboximport1     DataBox              Cancelled          6/26/2020 11:00:34 PM WestUS               myResourceGroup
myDataBoxOrderPSTest DataBox              Cancelled          7/07/2020 12:37:16 AM WestUS               myResourceGroup
mydataboxtest2       DataBox              Cancelled          6/10/2020 4:54:23  PM WestUS               myResourceGroup
mydataboxtest4       DataBox              DeviceOrdered      6/18/2020 3:48:00  AM WestUS               myResourceGroup
PS C:\WINDOWS\system32>
```

---

## <a name="cancel-the-order"></a>Cancelar o pedido

# <a name="portal"></a>[Portal](#tab/portal)

Para cancelar esse pedido, no portal do Azure, vá para **Visão geral** e selecione **Cancelar** na barra de comandos.

Depois de fazer um pedido, você poderá cancelá-lo a qualquer momento antes do status ser marcado como processado.

Para excluir um pedido cancelado, vá para **Visão geral** e selecione **Excluir** na barra de comandos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="cancel-an-order"></a>Cancelar um pedido

Para cancelar um pedido do Azure Data Box, execute [`az databox job cancel`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-cancel). Você precisa especificar o motivo para cancelar o pedido.

   ```azurecli
   az databox job cancel --resource-group <resource-group> --name <order-name> --reason <cancel-description>
   ```

   A seguinte tabela mostra as informações de parâmetro para `az databox job cancel`:

   | Parâmetro | Descrição |  Valor de exemplo |
   |---|---|---|
   |resource-group [Obrigatório]| O nome do grupo de recursos associado ao pedido a ser excluído. Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. | "myresourcegroup"|
   |name [Obrigatório]| O nome do pedido a ser excluído. | "mydataboxorder"|
   |reason [Obrigatório]| O motivo para cancelar o pedido. | "Inseri informações incorretas e precisei cancelar o pedido". |
   |sim| Não solicite confirmação. | --yes (-y)| 
   |depurar| Incluir informações de depuração para registro em log detalhado | --debug |
   |ajuda| Exibir informações de ajuda para esse comando. | --help -h |
   |only-show-errors| Mostrar apenas erros, suprimindo avisos. | --only-show-errors |
   |output -o| Define o formato de saída.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc. O valor padrão é json. | --output "json" |
   |Consulta| A cadeia de caracteres de consulta JMESPath. Para obter mais informações, confira [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Incluir o registro em log detalhado. | --verbose |

   Aqui está um exemplo do comando com a saída:

   ```azurecli
   PS C:\Windows> az databox job cancel --resource-group "myresourcegroup" --name "mydataboxtest3" --reason "Our budget was slashed due to **redacted** and we can no longer afford this device."
   ```

   Aqui está a saída da execução do comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   Are you sure you want to perform this operation? (y/n): y
   PS C:\Windows>
   ```

### <a name="delete-an-order"></a>Excluir um pedido

Se você cancelou um pedido do Azure Data Box, execute [`az databox job delete`](/cli/azure/ext/databox/databox/job#ext-databox-az-databox-job-delete) para excluir o pedido.

   ```azurecli
   az databox job delete --name [-n] <order-name> --resource-group <resource-group> [--yes] [--verbose]
   ```

   A seguinte tabela mostra as informações de parâmetro para `az databox job delete`:

   | Parâmetro | Descrição |  Valor de exemplo |
   |---|---|---|
   |resource-group [Obrigatório]| O nome do grupo de recursos associado ao pedido a ser excluído. Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. | "myresourcegroup"|
   |name [Obrigatório]| O nome do pedido a ser excluído. | "mydataboxorder"|
   |subscription| O nome ou a ID (GUID) da sua assinatura do Azure. | "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" |
   |sim| Não solicite confirmação. | --yes (-y)|
   |depurar| Incluir informações de depuração para registro em log detalhado | --debug |
   |ajuda| Exibir informações de ajuda para esse comando. | --help -h |
   |only-show-errors| Mostrar apenas erros, suprimindo avisos. | --only-show-errors |
   |output -o| Define o formato de saída.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc. O valor padrão é json. | --output "json" |
   |Consulta| A cadeia de caracteres de consulta JMESPath. Para obter mais informações, confira [JMESPath](http://jmespath.org/). | --query <string>|
   |verbose| Incluir o registro em log detalhado. | --verbose |

Aqui está um exemplo do comando com a saída:

   ```azurecli
   PS C:\Windows> az databox job delete --resource-group "myresourcegroup" --name "mydataboxtest3" --yes --verbose
   ```

   Aqui está a saída da execução do comando:

   ```output
   Command group 'databox job' is experimental and not covered by customer support. Please use with discretion.
   command ran in 1.142 seconds.
   PS C:\Windows>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-ps)

### <a name="cancel-an-order"></a>Cancelar um pedido

Para cancelar um pedido do Azure Data Box, execute [Stop-AzDataBoxJob](/powershell/module/az.databox/stop-azdataboxjob). Você precisa especificar o motivo para cancelar o pedido.

```azurepowershell
Stop-AzDataBoxJob -ResourceGroup <String> -Name <String> -Reason <String>
```

A seguinte tabela mostra as informações de parâmetro para `Stop-AzDataBoxJob`:

| Parâmetro | Descrição |  Valor de exemplo |
|---|---|---|
|ResourceGroup [obrigatório]| O nome do grupo de recursos associado ao pedido a ser cancelado. Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. | "myresourcegroup"|
|Name [obrigatório]| O nome do pedido a ser excluído. | "mydataboxorder"|
|Reason [obrigatório]| O motivo para cancelar o pedido. | "Inseri informações incorretas e precisei cancelar o pedido". |
|Force | Força o cmdlet a ser executado sem confirmação do usuário. | -Force |

Aqui está um exemplo do comando com a saída:

```azurepowershell
PS C:\PowerShell\Modules> Stop-AzDataBoxJob -ResourceGroupName myResourceGroup \
                                            -Name "myDataBoxOrderPSTest" \
                                            -Reason "I entered erroneous information and had to cancel."
```

Aqui está a saída da execução do comando:

```output
Confirm
"Cancelling Databox Job "myDataBoxOrderPSTest
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\WINDOWS\system32>
```

### <a name="delete-an-order"></a>Excluir um pedido

Se você cancelou um pedido do Azure Data Box, execute [`Remove-AzDataBoxJob`](/powershell/module/az.databox/remove-azdataboxjob) para excluir o pedido.

```azurepowershell
Remove-AzDataBoxJob -Name <String> -ResourceGroup <String>
```

A seguinte tabela mostra as informações de parâmetro para `Remove-AzDataBoxJob`:

| Parâmetro | Descrição |  Valor de exemplo |
|---|---|---|
|ResourceGroup [obrigatório]| O nome do grupo de recursos associado ao pedido a ser excluído. Um grupo de recursos é um contêiner lógico para os recursos que podem ser gerenciados ou implantados juntos. | "myresourcegroup"|
|Name [obrigatório]| O nome do pedido a ser excluído. | "mydataboxorder"|
|Force | Força o cmdlet a ser executado sem confirmação do usuário. | -Force |

Aqui está um exemplo do comando com a saída:

```azurepowershell
PS C:\Windows> Remove-AzDataBoxJob -ResourceGroup "myresourcegroup" \
                                   -Name "mydataboxtest3"
```

Aqui está a saída da execução do comando:

```output
Confirm
"Removing Databox Job "mydataboxtest3
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
PS C:\Windows>
```

---

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre artigos do Azure Data Box como:

> [!div class="checklist"]
>
> * Pré-requisitos para implantar o Data Box
> * Solicitar Data Box
> * Acompanhar o pedido
> * Cancelar o pedido

Avance para o próximo tutorial para saber como proteger seu Data Box.

> [!div class="nextstepaction"]
> [Configurar seu Azure Data Box](./data-box-deploy-set-up.md)
