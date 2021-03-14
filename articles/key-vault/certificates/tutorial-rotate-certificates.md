---
title: Tutorial – Atualizar a frequência de rotação automática do certificado no Key Vault | Microsoft Docs
description: Tutorial mostrando como atualizar a frequência de rotação automática de um certificado no Azure Key Vault usando o portal do Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 78cdc0b59fd178049e755cca5e1e909ac24483f2
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204041"
---
# <a name="tutorial-configure-certificate-auto-rotation-in-key-vault"></a>Tutorial: Configurar a rotação automática do certificado no Key Vault

Você pode provisionar, gerenciar e implantar facilmente os certificados digitais usando o Azure Key Vault. Os certificados podem ser certificados de protocolo TLS/SSL públicos ou privados assinados por uma AC (Autoridade de Certificação) ou um certificado autoassinado. O Key Vault também pode solicitar e renovar certificados por meio de parcerias com ACs, fornecendo uma solução robusta para o gerenciamento do ciclo de vida do certificado. Neste tutorial, você atualizará o período de validade, a frequência de rotação automática e os atributos da AC do certificado.

Este tutorial mostra como:

> [!div class="checklist"]
> * Gerenciar um certificado usando o portal do Azure.
> * Adicionar uma conta de provedor da AC.
> * Atualizar o período de validade do certificado.
> * Atualizar a frequência de rotação automática do certificado.
> * Atualizar os atributos do certificado usando o Azure PowerShell.

Antes de começar, leia [Conceitos básicos do Key Vault](../general/basic-concepts.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-vault"></a>Criar um cofre

Crie um Azure Key Vault usando o [portal do Azure](../general/quick-create-portal.md), a [CLI do Azure](../general/quick-create-cli.md) ou o [Azure PowerShell](../general/quick-create-powershell.md). No exemplo, o nome do cofre de chaves é **Example-Vault**.

![Saída após a conclusão da criação do cofre de chaves](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="create-a-certificate-in-key-vault"></a>Criar um certificado no Key Vault

Crie um certificado ou importe um certificado para o cofre de chaves (confira [Etapas para criar um certificado no Key Vault](../secrets/quick-create-portal.md)). Nesse caso, você trabalhará no certificado chamado **ExampleCertificate**.

## <a name="update-certificate-lifecycle-attributes"></a>Atualizar os atributos de ciclo de vida do certificado

No Azure Key Vault, você pode atualizar os atributos de ciclo de vida de um certificado antes e depois do momento da criação do certificado.

Um certificado criado no Key Vault pode ser:

- Um certificado autoassinado.
- Um certificado criado com uma AC parceira do Key Vault.
- Um certificado com uma AC que não é parceira do Key Vault.

As seguintes ACs são os provedores parceiros atuais do Key Vault:

- DigiCert: o Key Vault oferece certificados TLS/SSL OV.
- GlobalSign: o Key Vault oferece certificados TLS/SSL OV.

O Key Vault faz a rotação automática de certificados por meio de parcerias estabelecidas com ACs. Como o Key Vault solicita e renova certificados automaticamente por meio da parceria, a capacidade de rotação automática não é aplicável a certificados criados com ACs que não são parceiras do Key Vault.

> [!NOTE]
> Um administrador de conta de um provedor de AC cria credenciais que o Key Vault usa para criar, renovar e usar certificados TLS/SSL.
![Autoridade de certificação](../media/certificates/tutorial-rotate-cert/cert-authority-create.png)
>

### <a name="update-certificate-lifecycle-attributes-at-the-time-of-creation"></a>Atualizar atributos de ciclo de vida do certificado no momento da criação

1. Na página de propriedades do Key Vault, selecione **Certificados**.
1. Selecione **Gerar/Importar**.
1. Na tela **Criar um certificado**, atualize os seguintes valores:

   - **Período de Validade**: digite o valor (em meses). A criação de certificados de curta duração é uma prática de segurança recomendada. Por padrão, o valor da validade de um certificado recém-criado é de 12 meses.
   - **Tipo de Ação de Tempo de Vida**: selecione a ação de renovação automática e alerta do certificado e, em seguida, atualize **percentual de tempo de vida** ou **Número de dias antes da expiração**. Por padrão, a renovação automática de um certificado é definida em 80 por cento do tempo de vida dele. No menu suspenso, selecione uma das opções a seguir.

        |  Renovar automaticamente em um determinado momento| Enviar um email para todos os contatos em um determinado momento |
        |-----------|------|
        |Selecionar essa opção *ativará* rotação automática. | Selecionar essa opção *não* realizará a rotação automática, mas alertará os contatos.|

1. Selecione **Criar**.

![Ciclo de vida do certificado](../media/certificates/tutorial-rotate-cert/create-cert-lifecycle.png)

### <a name="update-lifecycle-attributes-of-a-stored-certificate"></a>Atualizar atributos de ciclo de vida do certificado armazenado

1. Selecione o cofre de chaves.
1. Na página de propriedades do Key Vault, selecione **Certificados**.
1. Selecione o certificado que você deseja atualizar. Nesse caso, você trabalhará no certificado chamado **ExampleCertificate**.
1. Selecione **Política de Emissão** na barra de menus superior.

   ![Captura de tela que realça o botão de Política de Emissão.](../media/certificates/tutorial-rotate-cert/cert-issuance-policy.png)

1. Na tela **Política de Emissão**, atualize os seguintes valores:

   - **Período de Validade**: atualize o valor (em meses).
   - **Tipo de Ação de Tempo de Vida**: selecione a ação de renovação automática e alerta do certificado e, em seguida, atualize **percentual de tempo de vida** ou **Número de dias antes da expiração**.

   ![Propriedades do certificado](../media/certificates/tutorial-rotate-cert/cert-policy-change.png)

1. Clique em **Salvar**.

> [!IMPORTANT]
> Alterar o Tipo de Ação de Tempo de Vida de um certificado registrará as modificações para os certificados existentes imediatamente.


### <a name="update-certificate-attributes-by-using-powershell"></a>Atualizar os atributos do certificado usando o PowerShell

```azurepowershell


Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName 
                                   -Name $certificateName 
                                   -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
```

> [!TIP]
> Para modificar a política de renovação de uma lista de certificados, insira `File.csv` contendo `VaultName,CertName` como no seguinte exemplo:
> <br/>
 `vault1,Cert1` <br/>
>  `vault2,Cert2`
>
>  ```azurepowershell
>  $file = Import-CSV C:\Users\myfolder\ReadCSVUsingPowershell\File.csv 
> foreach($line in $file)
> {
> Set-AzureKeyVaultCertificatePolicy -VaultName $vaultName -Name $certificateName -RenewAtNumberOfDaysBeforeExpiry [276 or appropriate calculated value]
> }
>  ```
> 
Para saber mais sobre os parâmetros, confira [az keyvault certificate](/cli/azure/keyvault/certificate#az-keyvault-certificate-set-attributes).

## <a name="clean-up-resources"></a>Limpar os recursos

Outros tutoriais do Key Vault se baseiam neste tutorial. Se você planeja trabalhar com esses tutoriais, talvez seja interessante deixar esses recursos existentes em vigor.
Quando você não precisar mais deles, exclua o grupo de recursos, que excluirá o cofre de chaves e os recursos relacionados.

Para excluir o grupo de recursos usando o portal:

1. Insira o nome do grupo de recursos na caixa **Pesquisa** na parte superior do portal. Quando o grupo de recursos usado neste início rápido for exibido nos resultados da pesquisa, selecione-o.
1. Selecione **Excluir grupo de recursos**.
1. Na caixa **DIGITE O NOME DO GRUPO DE RECURSOS:** , digite o nome do grupo de recursos e selecione **Excluir**.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você atualizou os atributos de ciclo de vida de um certificado. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os seguintes artigos:

- Leia mais sobre [Gerenciar a criação de certificados no Azure Key Vault](./create-certificate-scenarios.md).
- Examine a [Visão geral do Key Vault](../general/overview.md).
