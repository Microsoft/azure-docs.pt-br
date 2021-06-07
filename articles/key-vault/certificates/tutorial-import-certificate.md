---
title: Tutorial – Importar um certificado no Key Vault usando o portal do Azure | Microsoft Docs
description: Tutorial mostrando como importar um certificado no Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: 67531c1c5d37b02f9107dfc487c10b2fcac734d8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791959"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>Tutorial: Importar um certificado no Azure Key Vault

O Azure Key Vault é um serviço de nuvem que funciona como um repositório seguro de segredos. Você pode armazenar chaves, senhas, certificados e outros segredos com segurança. Os cofres de chaves do Azure podem ser criados e gerenciados por meio do portal do Azure. Neste tutorial, você criará um cofre de chaves e o usará para importar um certificado. Para saber mais sobre o Key Vault, analise a [Visão geral](../general/overview.md).

Este tutorial mostra como:

> [!div class="checklist"]
> * Crie um cofre da chave.
> * Importar um certificado no Key Vault usando o portal.
> * Importar um certificado no Key Vault usando a CLI.
> * Importar um certificado no Key Vault usando o PowerShell.


Antes de começar, leia [Conceitos básicos do Key Vault](../general/basic-concepts.md). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-vault"></a>Criar um cofre

1. No menu do portal do Azure ou na página **Início**, selecione **Criar um recurso**.
2. Digite **Key Vault** na caixa Pesquisar.
3. Na lista de resultados, escolha **Key Vault**.
4. Na seção Key Vault, escolha **Criar**.
5. A seção **Criar cofre de chaves** fornece as seguintes informações:
    - **Name**: um nome exclusivo é necessário. Para este início rápido, usaremos **Example-Vault**. 
    - **Assinatura**: Escolha uma assinatura.
    - Em **Grupo de Recursos**, escolha **Criar novo** e digite um nome para o grupo de recursos.
    - No menu suspenso **Local**, escolha um local.
    - Deixe as outras opções em seus padrões.
6. Depois de fornecer as informações acima, selecione **Criar**.

Anote as duas propriedades listadas abaixo:

* **Nome do cofre**: no exemplo, ele é **Example-Vault**. Você usará esse nome nas outras etapas.
* **URI do cofre**: no exemplo, é https://example-vault.vault.azure.net/. Aplicativos que usam seu cofre via API REST devem usar esse URI.

Nesse ponto, sua conta do Azure é a única autorizada a executar operações nesse novo cofre.

![Saída após a conclusão da criação do Key Vault](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>Importar um certificado ao Key Vault

Para importar um certificado para o cofre, você precisa ter um arquivo de certificado PEM ou PFX no disco. Neste caso, importaremos um certificado com o nome de arquivo chamado **ExampleCertificate**.

> [!IMPORTANT]
> No Azure Key Vault, os formatos de certificado compatíveis são PFX e PEM. 
> - O formato de arquivo .pem contém um ou mais arquivos de certificado X509.
> - O formato de arquivo .pfx é um formato de arquivo morto para armazenar vários objetos criptográficos em um só arquivo, ou seja, o certificado do servidor (emitido para seu domínio), uma chave privada correspondente e, opcionalmente, pode incluir uma AC intermediária.  

1. Na página de propriedades do Key Vault, selecione **Certificados**.
2. Clique em **Gerar/Importar**.
3. Na tela **Criar um certificado**, escolha os seguintes valores:
    - **Método de Criação de Certificado**: Importação.
    - **Nome do Certificado**: ExampleCertificate.
    - **Carregar o Arquivo de Certificado**: selecione o arquivo de certificado do disco
    - **Senha**: se você estiver carregando um arquivo de certificado protegido por senha, forneça essa senha aqui. Caso contrário, deixe em branco. Depois que o arquivo de certificado for importado com êxito, o cofre de chaves removerá essa senha.
4. Clique em **Criar**.

![Propriedades do certificado](../media/certificates/tutorial-import-cert/cert-import.png)

Ao adicionar um certificado usando o método de **Importação**, o Azure Key Vault preencherá automaticamente os parâmetros do certificado (ou seja, período de validade, nome do emissor, data de ativação etc.).

Depois de receber a mensagem indicando que o certificado foi importado com êxito, clique nele na lista para ver as propriedades dele. 

![Captura de tela que mostra o local para exibir as propriedades do certificado.](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Importar um certificado usando a CLI do Azure

Importe um certificado para um cofre de chaves especificado. Para importar um certificado válido existente, contendo uma chave privada, para o Azure Key Vault, o arquivo a ser importado pode estar no formato PFX ou PEM. Se o certificado estiver no formato PEM, o arquivo PEM deverá conter a chave, bem como os certificados x509. Essa operação requer a permissão certificados/importar.

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```

Saiba mais sobre os [parâmetros](/cli/azure/keyvault/certificate#az_keyvault_certificate_import).

Depois de importar o certificado, você poderá vê-lo usando [Mostrar certificado](/cli/azure/keyvault/certificate#az_keyvault_certificate_show)


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```

Agora você criou um cofre de chaves, importou um certificado e viu as propriedades dele.

## <a name="import-a-certificate-using-azure-powershell"></a>Importar um certificado usando o Azure PowerShell

```
Import-AzureKeyVaultCertificate
      [-VaultName] <String>
      [-Name] <String>
      -FilePath <String>
      [-Password <SecureString>]
      [-Tag <Hashtable>]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```

Saiba mais sobre os [parâmetros](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?).


## <a name="clean-up-resources"></a>Limpar os recursos

Outros tutoriais e inícios rápidos do Key Vault complementam este início rápido. Se você planeja continuar a trabalhar com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.
Quando não for mais necessário, exclua o grupo de recursos, que excluirá o Key Vault e os recursos relacionados. Para excluir o grupo de recursos pelo portal:

1. Insira o nome do grupo de recursos na caixa de pesquisa na parte superior do portal. Quando você vir o grupo de recursos usado neste início rápido nos resultados da pesquisa, selecione-o.
2. Selecione **Excluir grupo de recursos**.
3. Na caixa **DIGITE O NOME DO GRUPO DE RECURSOS:** , digite o nome do grupo de recursos e selecione **Excluir**.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um Key Vault e importou um certificado nele. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia mais sobre [Gerenciar a criação de certificados no Azure Key Vault](./create-certificate-scenarios.md)
- Veja exemplos de [Como importar certificados usando APIs REST](/rest/api/keyvault/importcertificate/importcertificate)
- Examinar a [Visão geral de segurança do Key Vault](../general/security-overview.md)