---
title: Tutorial`:` Usar uma identidade gerenciada para acessar o Azure Key Vault – Linux – Azure AD
description: Um tutorial que o conduz pelo processo de usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar o Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb31d6e25ce1c1ff5c3e4dbabb4fa53da0bd2ef3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093925"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Tutorial: Usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar o Azure Key Vault 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Este tutorial mostra como uma VM (máquina virtual) do Linux pode usar uma identidade gerenciada atribuída pelo sistema para acessar o [Azure Key Vault](../../key-vault/general/overview.md). Atuando como uma inicialização, o Key Vault possibilita que o aplicativo cliente use o segredo para acessar recursos não protegidos pelo Azure AD (Active Directory). As Identidades de Serviço Gerenciadas são gerenciadas automaticamente pelo Azure e permitem a você autenticar os serviços compatíveis com a autenticação do Azure AD sem incluir informações de autenticação em seu código.

Você aprenderá como:

> [!div class="checklist"]
> * Conceda o acesso da máquina virtual a um segredo armazenado em um Key Vault 
> * Obtenha um token de acesso usando a identidade da VM e use-o para recuperar o segredo do Key Vault 
 
## <a name="prerequisites"></a>Pré-requisitos

- Conhecimento básico de identidades gerenciadas. Se você não estiver familiarizado com as identidades gerenciadas para funcionalidades de recursos do Azure, veja esta [visão geral](overview.md). 
- Uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/).
- Permissões de "Proprietário" no escopo apropriado (sua assinatura ou grupo de recursos) para executar a criação de recursos e o gerenciamento de função necessários. Se precisar de ajuda com a atribuição de função, confira [Atribuir funções do Azure para gerenciar o acesso aos recursos de assinatura do Azure](../../role-based-access-control/role-assignments-portal.md).
- Você também precisa de uma Máquina Virtual do Linux com as identidades gerenciadas atribuídas ao sistema habilitadas.
  - Se precisar criar uma máquina virtual para este tutorial, siga o artigo intitulado [Criar uma máquina virtual do Linux com o portal do Azure](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


## <a name="create-a-key-vault"></a>Criar um cofre de chaves  

Esta seção mostra como conceder à sua VM acesso a um segredo armazenado em um Key Vault. Usando a identidades gerenciadas para recursos do Azure, seu código pode obter tokens de acesso para autenticar para recursos que oferecem suporte à autenticação do Azure AD.No entanto, nem todos os serviços do Azure dão suporte à autenticação do Azure AD. Para usar identidades gerenciadas para recursos do Azure com esses serviços, armazene as credenciais de serviço no Azure Key Vault e use a identidade gerenciada da VM para acessar o Key Vault para recuperar as credenciais.

Primeiro, precisamos criar um cofre de chaves e conceder à nossa identidade gerenciada atribuída pelo sistema da VM acesso ao Key Vault.

1. Abra o [portal](https://portal.azure.com/) do Azure
1. Na parte superior da barra de navegação esquerda, selecione **Criar um recurso**  
1. Na caixa **Pesquisar no Marketplace**, digite **Key Vault** e pressione **Enter**.  
1. Selecione **Key Vault** nos resultados.
1. Escolha **Criar**
1. Forneça um **Nome** para o novo Key Vault.

    ![Criar uma tela do cofre de chaves](./media/tutorial-linux-vm-access-nonaad/create-key-vault.png)

1. Preencha todas as informações necessárias, escolhendo a assinatura e o grupo de recursos em que você criou a máquina virtual que está usando para este tutorial.
1. Selecione **Examinar + criar**
1. Escolha **Criar**

### <a name="create-a-secret"></a>Criar um segredo

Em seguida, adicione um segredo ao Key Vault, para que você possa recuperá-lo posteriormente usando o código em execução na sua VM. Para este tutorial, estamos usando o PowerShell, mas os mesmos conceitos se aplicam a qualquer código em execução nesta máquina virtual.

1. Navegue até o Key Vault recém-criado.
1. Selecione **Segredos** e clique em **Adicionar**.
1. Selecione **Gerar/Importar**
1. Na tela **Criar um segredo** de **Opções de upload** deixe **Manual** selecionado.
1. Insira um nome e um valor para o segredo.  O valor pode ser qualquer coisa que você desejar. 
1. Deixe a data de ativação e a data de validade em branco e deixe **Habilitado** como **Sim**. 
1. Clique em **Criar** para criar o segredo.

   ![Criar um segredo](./media/tutorial-linux-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>Conceder acesso

A identidade gerenciada usada pela máquina virtual precisa receber acesso para ler o segredo que armazenaremos no Key Vault.

1. Navegue até o Key Vault recém-criado
1. Selecione **Política de Acesso** no menu no lado esquerdo.
1. Selecione **Adicionar política de acesso**

   ![tela de criação de política de acesso do cofre de chaves](./media/tutorial-linux-vm-access-nonaad/key-vault-access-policy.png)

1. Na seção **Adicionar política de acesso**, em **Configurar com base no modelo (opcional)** , escolha **Gerenciamento de Segredo** no menu suspenso.
1. Escolha **Selecionar Entidade de Segurança** e no campo de pesquisa insira o nome da VM que você criou anteriormente.  Selecione a VM na lista de resultados e escolha **Selecionar**.
1. Selecione **Adicionar**
1. Selecione **Salvar**.

## <a name="access-data"></a>Acessar dados

Para concluir essas etapas, você precisará do cliente SSH.  Se você estiver usando o Windows, poderá usar o cliente SSH no [Subsistema do Windows para Linux](/windows/wsl/about). Se precisar de ajuda para configurar as chaves do cliente SSH, confira [Como usar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md), ou [Como criar e usar um par de chaves SSH pública e privada para VMs Linux no Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. No portal, navegue até a VM do Linux e em **Visão geral**, clique em **Conectar**. 
2. **Conecte-se** à VM com um cliente SSH de sua escolha. 
3. Na janela de terminal, usando CURL, faça uma solicitação para as identidades gerenciadas locais para o ponto de extremidade de recursos do Azure para obter um token de acesso para o Azure Key Vault.  
 
    A solicitação CURL para o token de acesso está abaixo.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    A resposta inclui o token de acesso de que você precisa para acessar o Azure Resource Manager. 
    
    Resposta:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Você pode usar esse token de acesso para autenticar o Azure Key Vault.  A próxima solicitação CURL mostra como ler um segredo do Key Vault usando CURL e a API REST do Key Vault.  Você precisará da URL de seu Key Vault, que está na seção **Essentials** da página **Visão geral** do Key Vault.  O token de acesso que você obteve na chamada anterior também será necessário. 
        
    ```bash
    curl 'https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    A resposta terá a seguinte aparência: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Depois de recuperar o segredo do Key Vault, você pode usá-lo para autenticar um serviço que requer um nome e uma senha.

## <a name="clean-up-resources"></a>Limpar recursos

Quando desejar limpar os recursos, acesse o [portal do Azure](https://portal.azure.com), selecione **Grupos de recursos**, localize e selecione o grupo de recursos criado no processo deste tutorial (como `mi-test`) e use o comando **Excluir grupo de recursos**.

Como alternativa, você também pode fazer isso por meio do [PowerShell ou da CLI](../../azure-resource-manager/management/delete-resource-group.md)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a usar uma identidade gerenciada atribuída pelo sistema da VM do Linux para acessar o Azure Key Vault.  Para saber mais sobre o Azure Key Vault, confira:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)