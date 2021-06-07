---
title: Como criar e mesclar uma CSR no Azure Key Vault
description: Saiba como criar e mesclar uma CSR no Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: a4d079855e5aa05adb84b62d686d9f386608f7bb
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752130"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>Criar e mesclar uma CSR no Key Vault

O Azure Key Vault dá suporte ao armazenamento de certificados digitais emitidos por qualquer AC (autoridade de certificação). Ele dá suporte à criação de uma CSR (solicitação de assinatura de certificado) com um par de chaves privada/pública. A CSR pode ser assinada por qualquer AC (uma AC corporativa interna ou uma AC pública externa). Uma CSR é uma mensagem que você envia a uma AC para solicitar um certificado digital.

Para obter mais informações gerais sobre certificados, confira [Certificados do Azure Key Vault](./about-certificates.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>Adicionar certificados no Key Vault emitido por ACs parceiras

Parceiros de Key Vault com as seguintes autoridades de certificação para simplificar a criação de certificado.

|Provedor|Tipo de certificado|Configuração  
|--------------|----------------------|------------------|  
|DigiCert|O Key Vault oferece certificados SSL OV ou EV com DigiCert| [Guia de integração](./how-to-integrate-certificate-authority.md)
|GlobalSign|O Key Vault oferece certificados SSL OV ou EV com GlobalSign| [Guia de integração](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>Adicionar certificados no Key Vault emitidos por ACs não parceiras

Siga estas etapas para adicionar um certificado de ACs que não são parceiros com Key Vault. (Por exemplo, GoDaddy não é uma AC do Key Vault confiável.)

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Vá para o cofre de chaves ao qual você deseja adicionar o certificado.
1. Na página Propriedades, selecione **Certificados**.
1. Selecione a guia **Gerar/Importar**.
1. Na tela **Criar um certificado**, escolha os seguintes valores:
    - **Método de Criação de Certificado**: Generate.
    - **Nome do Certificado**: ContosoManualCSRCertificate.
    - **Tipo de AC (Autoridade de Certificação)** : Certificado emitido por uma AC não integrada.
    - **Entidade:** `"CN=www.contosoHRApp.com"`.
     > [!NOTE]
     > Se estiver usando um RDN (nome diferenciado relativo) que tenha uma vírgula (,) no valor, encapsule o valor que contém o caractere especial entre aspas duplas. 
     >
     > Exemplo de entrada para **Entidade**: `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > Neste exemplo, o RDN `OU` contém um valor com uma vírgula no nome. A saída resultante de `OU` é **Docs, Contoso**.
1. Selecione os outros valores conforme desejado e escolha **Criar** para adicionar o certificado à lista **Certificados**.

    ![Captura de tela das propriedades do certificado](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. Na lista **Certificados**, selecione o novo certificado. O estado atual do certificado é **desabilitado** porque ele ainda não foi emitido pela AC.
1. Na guia **Operação de Certificado**, selecione **Baixar a CSR**.

   ![Captura de tela que realça o botão Baixar CSR.](../media/certificates/create-csr-merge-csr/download-csr.png)

1. Faça com que a AC assine a CSR (.csr).
1. Depois que a solicitação for assinada, selecione **Mesclar Solicitação Assinada** na guia **Operação de Certificado** para adicionar o certificado assinado ao Key Vault.

A solicitação de certificado foi mesclada com êxito.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Crie uma política de certificação. Como a AC escolhida neste cenário não é parceira, **IssuerName** é definido como **Desconhecido** e o Key Vault não registra nem renova o certificado.

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > Se estiver usando um RDN (nome diferenciado relativo) que tenha uma vírgula (,) no valor, use aspas simples para o valor completo ou conjunto de valores e coloque o valor que contém o caractere especial entre aspas duplas. 
     >
     >Exemplo de entrada para **SubjectName**: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown`. Neste exemplo, o valor `OU` é lido como **Docs, Contoso**. Esse formato funciona para todos os valores que contêm uma vírgula.
     > 
     > Neste exemplo, o RDN `OU` contém um valor com uma vírgula no nome. A saída resultante de `OU` é **Docs, Contoso**.

1. Crie a CSR.

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. Faça com que a AC assine a CSR. O `$csr.CertificateSigningRequest` é a CSR codificada de base para o certificado. Você pode despejar esse blob no site de solicitação de certificado do emissor. Essa etapa varia entre as ACs. Confira as diretrizes da AC sobre como executar essa etapa. Você também pode usar ferramentas como certreq ou openssl para obter a CSR assinada e concluir o processo de geração de um certificado.

1. Mescle a solicitação assinada no Key Vault. Depois que a solicitação de certificado tiver sido assinada, você poderá mesclá-la ao par de chaves privada/pública inicial criado no Azure Key Vault.

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

A solicitação de certificado foi mesclada com êxito.

---

## <a name="add-more-information-to-the-csr"></a>Adicionar mais informações à CSR

Se você quiser adicionar mais informações ao criar a CSR, defina-as no **SubjectName**. Talvez você queira adicionar informações como:
- País
- Cidade/localidade
- Estado/Província
- Organização
- Unidade organizacional

Exemplo

   ```azure-powershell
   SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
   ```

> [!NOTE]
> Se você estiver solicitando um certificado de DV (validação de domínio) com informações adicionais, a AC poderá rejeitar a solicitação se não for possível validar todas as informações na solicitação. As informações adicionais poderão ser mais apropriadas se você estiver solicitando um certificado de validação de organização (OV).

## <a name="faqs"></a>Perguntas frequentes

- Como monitorar ou gerenciar minha CSR?

     Confira [Monitorar e gerenciar a criação de certificados](./create-certificate-scenarios.md).

- E se eu vir **Tipo de erro 'A chave pública do certificado de entidade final no conteúdo do certificado X. 509 especificado não corresponde à parte pública da chave privada especificada. Verifique se o certificado é válido'** ?

     Esse erro ocorrerá se você não estiver mesclando a CSR assinada à mesma solicitação de CSR iniciada. Cada CSR que você cria tem uma chave privada, que precisa corresponder ao mesclar a solicitação assinada.

- Quando uma CSR é mesclada, ela mescla toda a cadeia?

     Sim, ele mescla toda a cadeia, desde que o usuário tenha fornecido um arquivo p7b para mesclagem.

- E se o certificado emitido estiver em status desabilitado no portal do Azure?

     Veja a guia **Operação de Certificado** para examinar a mensagem de erro para esse certificado.

- E se eu encontrar o **Tipo de erro "O nome da entidade fornecido não é um nome X500 válido"** ?

     Esse erro pode ocorrer se **SubjectName** incluir caracteres especiais. Confira as notas nas instruções portal do Azure e do PowerShell.

---

## <a name="next-steps"></a>Próximas etapas

- [Autenticação, solicitações e respostas](../general/authentication-requests-and-responses.md)
- [Guia do Desenvolvedor do Cofre de Chaves](../general/developers-guide.md)
- [Referência da API REST do Azure Key Vault](/rest/api/keyvault)
- [Cofres – criar ou atualizar](/rest/api/keyvault/vaults/createorupdate)
- [Cofres – atualizar política de acesso](/rest/api/keyvault/vaults/updateaccesspolicy)