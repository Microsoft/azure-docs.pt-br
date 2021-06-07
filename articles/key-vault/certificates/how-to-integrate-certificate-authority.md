---
title: Como integrar o Key Vault à autoridade de certificação DigiCert
description: Como integrar o Key Vault à autoridade de certificação DigiCert
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: 4351526c77961856b118bdeae07cecf48340f5fe
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749304"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Como integrar o Key Vault à autoridade de certificação DigiCert

O Azure Key Vault permite que você provisione, gerencie e implante facilmente certificados digitais para sua rede e habilite comunicações seguras para aplicativos. Um certificado digital é uma credencial eletrônica para estabelecer uma prova de identidade em uma transação eletrônica. 

Os usuários do Azure Key Vault podem gerar certificados DigiCert diretamente do Key Vault. O Key Vault garante o gerenciamento de ciclo de vida do certificado de ponta a ponta para os certificados emitidos pela DigiCert por meio da parceria confiável do Key Vault com a autoridade de certificação DigiCert.

Para obter mais informações gerais sobre certificados, confira [Certificados do Azure Key Vault](./about-certificates.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia, você deve ter os recursos a seguir.
* Um cofre de chaves. Você pode usar um cofre de chaves existente ou criar um seguindo as etapas em um destes inícios rápidos:
   - [Criar um cofre de chaves com a CLI do Azure](../general/quick-create-cli.md)
   - [Criar um cofre de chaves com o Azure PowerShell](../general/quick-create-powershell.md)
   - [Criar um cofre de chaves com o portal do Azure](../general/quick-create-portal.md).
*   Você precisa ativar a conta DigiCert CertCentral. [Inscreva-se](https://www.digicert.com/account/signup/) para sua conta do CertCentral.
*   Permissões em nível de administrador em suas contas.


### <a name="before-you-begin"></a>Antes de começar

Verifique se você tem as seguintes informações disponíveis sobre sua conta DigiCert CertCentral:
-   ID da conta CertCentral
-   ID da organização
-   Chave de API

## <a name="adding-certificate-authority-in-key-vault"></a>Como adicionar a autoridade de certificação no Key Vault 
Depois de coletar as informações acima da conta do DigiCert CertCentral, agora você pode adicionar DigiCert à lista de autoridades de certificação no cofre de chaves.

### <a name="azure-portal"></a>Portal do Azure

1.  Para adicionar a autoridade de certificação DigiCert, navegue até o cofre de chaves ao qual você deseja adicionar DigiCert. 
2.  Na página de propriedades do Key Vault, selecione **Certificados**.
3.  Selecione a guia **Autoridades de Certificação**. ![Selecione autoridades de certificação](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  Selecione a opção **Adicionar**.
 ![adicionar autoridades de certificação](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  Na tela **Criar uma autoridade de certificado**, escolha os seguintes valores:
    -   **Name**: Adicione um nome de emissor identificável. Exemplo de DigicertCA
    -   **Provedor**: selecione DigiCert no menu.
    -   **ID da Conta**: insira sua ID da conta do DigiCert CertCentral
    -   **Senha da Conta**: insira a chave de API que você gerou em sua conta do DigiCert CertCentral
    -   **ID da organização**: insira a OrgID coletada da conta DigiCert CertCentral 
    -   Clique em **Criar**.
   
6.  Você verá que a DigicertCA agora foi adicionada à lista de autoridades de certificação.


### <a name="azure-powershell"></a>Azure PowerShell

O Azure PowerShell é usado para criar e gerenciar recursos do Azure usando comandos ou scripts. O Azure hospeda o Azure Cloud Shell, um ambiente de shell interativo que pode ser usado por meio do portal do Azure no próprio navegador.

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 1.0.0 ou posterior. Digite `$PSVersionTable.PSVersion` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzAccount` para criar uma conexão com o Azure.

```azurepowershell-interactive
Login-AzAccount
```

1.  Criar um **grupo de recursos**

Crie um grupo de recursos do Azure com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. Criar um **Key Vault**

Use um nome exclusivo para seu cofre de chaves. Aqui, "Contoso-Vaultname" é o nome para Key Vault em todo este guia.

- **Nome do cofre** Contoso-Vaultname.
- **Nome do grupo de recursos** ContosoResourceGroup.
- **Local** EastUS.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. Defina variáveis para obter informações coletadas da conta DigiCert CertCentral.

- Definir a variável **ID da Conta**
- Definir a variável **ID da Organização**
- Definir a variável **Chave de API**

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzKeyVaultCertificateOrganizationDetail -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
```

4. Defina o **Emissor**. Isso adicionará DigiCert como uma autoridade de certificação no cofre de chaves. Para saber mais sobre os parâmetros, [leia aqui](/powershell/module/az.keyvault/Set-AzKeyVaultCertificateIssuer)
```azurepowershell-interactive
Set-AzKeyVaultCertificateIssuer -VaultName "Contoso-Vaultname" -Name "TestIssuer01" -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org -PassThru
```

5. **Configurar a política para o certificado e o certificado de emissão** de DigiCert diretamente dentro do Key Vault.

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "TestIssuer01" -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

O certificado foi emitido com êxito pela AC DigiCert dentro do Key Vault especificado por meio dessa integração.

## <a name="troubleshoot"></a>Solucionar problemas

Se o certificado emitido estiver no status "desabilitado" no portal do Azure, prossiga para exibir a **Operação de Certificado** para examinar a mensagem de erro DigiCert para esse certificado.

 ![Operação de certificado](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

Mensagem de erro 'Faça uma mesclagem para concluir esta solicitação de certificado'.
Você precisará mesclar o CSR assinado pela AC para concluir essa solicitação. Saiba mais [aqui](./create-certificate-signing-request.md)

Para obter mais informações, veja [Operações de certificado na referência de API REST do Key Vault](/rest/api/keyvault). Para obter informações sobre como estabelecer permissões, confira [Cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate) e [Cofres – atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

- Posso gerar um certificado curinga DigiCert por meio do Key Vault? 
   Sim. Isso depende de como você configurou sua conta do DigiCert.
- Como posso criar o certificado **OV-SSL ou EV-SSL** com o DigiCert? 
   O cofre de chaves dá suporte à criação de certificados SSL OV e EV. Ao criar um certificado, clique em Configuração de Política Avançada e especifique o Tipo de certificado. Os valores compatíveis são: OV-SSL e EV-SSL
   
   Você poderia criar esse tipo de certificado no cofre de chaves se a sua conta do Digicert permitisse. Para esse tipo de certificado, a validação é executada pelo DigiCert e a equipe de suporte dele será capaz de ajudar você com a solução se a validação falhar. Você pode adicionar mais informações ao criar um certificado definindo-as em subjectName.

Exemplo
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```
   
- Há um atraso de tempo na criação do certificado DigiCert por meio da integração comparado à aquisição do certificado diretamente por meio da DigiCert?
   Não. Ao criar um certificado, é o processo de verificação que pode levar algum tempo e essa verificação depende do processo seguido pela DigiCert.


## <a name="next-steps"></a>Próximas etapas

- [Autenticação, solicitações e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Desenvolvedor do Cofre de Chaves](../general/developers-guide.md)