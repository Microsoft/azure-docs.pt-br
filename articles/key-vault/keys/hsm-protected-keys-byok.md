---
title: Como gerar e transferir as chaves protegidas por HSM – BYOK – Azure Key Vault
description: Use este artigo para ajudar você a planejar, gerar e transferir as próprias chaves protegidas por HSM a serem usadas com o Azure Key Vault. Também conhecido como BYOK (Bring Your Own Key).
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: f1b5d6499594e9026e1615be5361c52c9ce2f4ef
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774801"
---
# <a name="import-hsm-protected-keys-to-key-vault-byok"></a>Importar chaves protegidas por HSM para o Key Vault (BYOK)

Para garantia extra, ao usar o Azure Key Vault, você pode importar ou gerar uma chave em um HSM (módulo de segurança de hardware); a chave nunca sairá do limite do HSM. Esse cenário é, muitas vezes, conhecido como BYOK (*Bring Your Own Key*). O Key Vault usa a família nCipher nShield de HSMs (com validação FIPS 140-2 Nível 2) para proteger suas chaves.

Use as informações deste artigo para ajudar você a planejar, gerar e transferir as próprias chaves protegidas por HSM a serem usadas com o Azure Key Vault.

> [!NOTE]
> Essa funcionalidade não está disponível para a Azure China 21Vianet. 
> 
> Esse método de importação está disponível somente para [HSMs compatíveis](#supported-hsms). 

Para obter mais informações e obter um tutorial de introdução ao uso do Key Vault (incluindo como criar um cofre de chaves para chaves protegidas por HSM), confira [O que é o Azure Key Vault?](../general/overview.md).

## <a name="overview"></a>Visão geral

Eis uma visão geral do processo. As etapas específicas a serem concluídas serão descritas mais adiante neste artigo.

* No Key Vault, gere uma chave (chamada de *KEK* [Chave de Troca de Chaves]). A KEK precisa ser uma chave RSA-HSM que tenha apenas a operação de chave `import`. Somente o SKU Premium do Key Vault dá suporte a chaves RSA-HSM.
* Baixe a chave pública KEK como um arquivo .pem.
* Transfira a chave pública KEK para um computador offline que esteja conectado a um HSM local.
* No computador offline, use a ferramenta BYOK disponibilizada pelo fornecedor do HSM para criar um arquivo BYOK. 
* A chave de destino é criptografada com uma KEK, que permanece criptografada até ser transferida para o HSM do Key Vault. Apenas a versão criptografada da chave sai do HSM local.
* Uma KEK gerada em um HSM do Key Vault não é exportável. Os HSMs impõem a regra de que não existe nenhuma versão clara de uma KEK fora de um HSM do Key Vault.
* A KEK precisa estar no mesmo cofre de chaves em que a chave de destino será importada.
* Quando o arquivo BYOK é carregado para o Key Vault, um HSM do Key Vault usa a chave privada KEK para descriptografar o material de chave de destino e importá-lo como uma chave HSM. Essa operação ocorre inteiramente em um HSM do Key Vault. A chave de destino sempre permanece no limite de proteção do HSM.

## <a name="prerequisites"></a>Pré-requisitos

A seguinte tabela lista os pré-requisitos para o uso do BYOK no Azure Key Vault:

| Requisito | Mais informações |
| --- | --- |
| Uma assinatura do Azure |Para criar um cofre de chaves no Azure Key Vault, você precisará ter uma assinatura do Azure. [Inscreva-se em uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/). |
| Um SKU Premium do Key Vault para importar as chaves protegidas por HSM |Para obter mais informações sobre as camadas de serviço e as funcionalidades do Azure Key Vault, confira [Preços do Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Um HSM da lista de HSMs compatíveis e uma ferramenta BYOK, além das instruções disponibilizadas pelo fornecedor do HSM | Você precisará ter permissões em um HSM e conhecimento básico de como usá-lo. Confira [HSMs compatíveis](#supported-hsms). |
| CLI do Azure versão 2.1.0 ou posterior | Confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).|

## <a name="supported-hsms"></a>HSMs compatíveis

|Nome do fornecedor|Tipo de fornecedor|Modelos de HSM compatíveis|Mais informações|
|---|---|---|---|
|nCipher|Fabricante,<br/>HSM como serviço|<ul><li>Família de HSMs nShield</li><li>nShield como serviço</ul>|[Nova ferramenta BYOK nCipher e documentação](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Fabricante|<ul><li>Família HSM 7 da Luna com versão de firmware 7.3 ou mais recente</li></ul>| [Ferramenta BYOK Luna e documentação](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Fabricante,<br/>HSM como serviço|<ul><li>SDKMS (Serviço de Gerenciamento de Chaves de Proteção Automática)</li><li>Equinix SmartKey</li></ul>|[Como exportar chaves SDKMS para provedores de nuvem para BYOK – Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Fabricante|Todos os HSMs LiquidSecurity com<ul><li>Firmware versão 2.0.4 ou posterior</li><li>Firmware versão 3.2 ou mais recente</li></ul>|[Ferramenta BYOK Marvell e documentação](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (Sistema de Gerenciamento de Chaves Empresariais)|Várias marcas e modelos de HSM, incluindo<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Acesse o [site da Cryptomathic para obter detalhes](https://www.cryptomathic.com/azurebyok)|[Ferramenta BYOK Cryptomathic e documentação](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Fabricante,<br/>HSM como serviço|Família HSM Primus, Securosys Clouds HSM|[Ferramenta e documentação do Primus BYOK](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Sistema de Gerenciamento de Chaves Empresariais)|Várias marcas e modelos de HSM, incluindo<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Confira o [site da StorMagic para obter detalhes](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS e Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Fabricante|IBM 476x, CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Fabricante,<br/>HSM como serviço|Âncora u.trust, CryptoServer|[Ferramenta BYOK e Guia de integração da Ultimaco](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Tipos de chave com suporte

|Nome da chave|Tipo de chave|Tamanho/curva da chave|Origem|Descrição|
|---|---|---|---|---|
|KEK (Chave de Troca de Chaves)|RSA| 2\.048 bits<br />3\.072 bits<br />4\.096 bits|HSM do Azure Key Vault|Um par de chaves RSA compatível com HSM gerado no Azure Key Vault|
|Chave de destino|
||RSA|2\.048 bits<br />3\.072 bits<br />4\.096 bits|HSM do fornecedor|A chave a ser transferida para o HSM do Azure Key Vault|
||EC|P-256<br />P-384<br />P-521|HSM do fornecedor|A chave a ser transferida para o HSM do Azure Key Vault|
||||

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>Gerar e transferir sua chave para o HSM do Key Vault

Para gerar e transferir sua chave para um HSM do Key Vault:

* [Etapa 1: Gerar uma KEK](#step-1-generate-a-kek)
* [Etapa 2: Baixar a chave pública KEK](#step-2-download-the-kek-public-key)
* [Etapa 3: Gerar e preparar sua chave para transferência](#step-3-generate-and-prepare-your-key-for-transfer)
* [Etapa 4: Transferir sua chave para o Azure Key Vault](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>Etapa 1: Gerar uma KEK

Uma KEK é uma chave RSA que é gerada em um HSM do Key Vault. A KEK é usada para criptografar a chave que você deseja importar (a chave de *destino*).

A KEK precisa ser:
- Uma chave RSA-HSM (2.048 bits, 3.072 bits ou 4.096 bits)
- Gerada no mesmo cofre de chaves em que você pretende importar a chave de destino
- Criada com operações de chave permitidas definidas como `import`

> [!NOTE]
> A KEK precisa ter 'import' como a única operação de chave permitida. 'import' é mutuamente exclusiva em relação a todas as outras operações de chave.

Use o comando [az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create) para criar uma KEK que tenha operações de chave definidas como `import`. Registre o identificador de chave (`kid`) que é retornado do comando a seguir. (Você usará o valor `kid` na [Etapa 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>Etapa 2: Baixar a chave pública KEK

Use [az keyvault key download](/cli/azure/keyvault/key#az_keyvault_key_download) para baixar a chave pública KEK para um arquivo .pem. A chave de destino que você importa é criptografada com a chave pública KEK.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

Transfira o arquivo KEKforBYOK.publickey.pem para o computador offline. Você precisará dele na próxima etapa.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Etapa 3: Gerar e preparar sua chave para transferência

Veja a documentação do fornecedor do HSM para baixar e instalar a ferramenta BYOK. Siga as instruções do fornecedor do HSM para gerar uma chave de destino e crie um pacote de transferência de chave (um arquivo BYOK). A ferramenta BYOK usará o `kid` da [Etapa 1](#step-1-generate-a-kek) e o arquivo KEKforBYOK.publickey.pem que você baixou na [Etapa 2](#step-2-download-the-kek-public-key) para gerar uma chave de destino criptografada em um arquivo BYOK.

Transfira o arquivo BYOK para o computador conectado.

> [!NOTE] 
> Não há suporte para a importação de chaves RSA de 1.024 bits. Não há suporte para a importação da chave de curva elíptica com a curva P-256K.
> 
> **Problema conhecido**: Só há suporte para a importação de uma chave de destino RSA de 4 mil bits de HSMs do Luna no firmware 7.4.0 ou mais recente.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>Etapa 4: Transferir sua chave para o Azure Key Vault

Para concluir a importação da chave, transfira o pacote de transferência de chave (um arquivo BYOK) do computador desconectado para o computador conectado à Internet. Use o comando [az keyvault key import](/cli/azure/keyvault/key#az_keyvault_key_import) para carregar o arquivo BYOK para o HSM do Key Vault.

Para importar uma chave RSA, use o comando a seguir. O parâmetro --kty é opcional e assume como padrão 'RSA-HSM'.
```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Para importar uma chave de curva elíptica, você precisa especificar o tipo de chave e o nome da curva.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file --kty EC-HSM --curve-name "P-256" KeyTransferPackage-ContosoFirstHSMkey.byok
```

Se o upload for bem-sucedido, a CLI do Azure exibirá as propriedades da chave importada.

## <a name="next-steps"></a>Próximas etapas

Agora você pode usar essa chave de HSM protegido no Cofre da Chave. Para obter mais informações, confira [esta comparação de preços e recursos](https://azure.microsoft.com/pricing/details/key-vault/).
