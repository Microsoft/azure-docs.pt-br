---
title: Como gerar e transferir chaves protegidas por HSM para Azure Key Vault HSM gerenciado-Azure Key Vault | Microsoft Docs
description: Use este artigo para ajudá-lo a planejar, gerar e transferir suas próprias chaves protegidas por HSM para usar com o HSM gerenciado. Também conhecido como BYOK (Bring Your Own Key).
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: ambapat
ms.openlocfilehash: dd5b38a858ceba12f5d48f1782da5b85228c4b06
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212103"
---
# <a name="import-hsm-protected-keys-to-managed-hsm-byok"></a>Importar chaves protegidas por HSM para HSM gerenciado (BYOK)

 Azure Key Vault HSM gerenciado dá suporte à importação de chaves geradas no módulo de segurança de hardware local (HSM); as chaves nunca deixarão o limite de proteção do HSM. Esse cenário é, muitas vezes, conhecido como BYOK (*Bring Your Own Key*). O HSM gerenciado usa os adaptadores HSM da Marvell LiquidSecurity (FIPS 140-2 nível 3 validados) para proteger suas chaves.

Use as informações neste artigo para ajudá-lo a planejar, gerar e transferir suas próprias chaves protegidas por HSM para usar com o HSM gerenciado.

> [!NOTE]
> Essa funcionalidade não está disponível para a Azure China 21Vianet. Esse método de importação está disponível somente para [HSMs compatíveis](#supported-hsms). 

Para obter mais informações e para obter um tutorial para começar a usar o HSM gerenciado, consulte [o que é o HSM gerenciado?](overview.md).

## <a name="overview"></a>Visão geral

Eis uma visão geral do processo. As etapas específicas a serem concluídas serão descritas mais adiante neste artigo.

* No HSM gerenciado, gere uma chave (chamada de chave de *troca de chaves* (Kek)). A KEK precisa ser uma chave RSA-HSM que tenha apenas a operação de chave `import`. 
* Baixe a chave pública KEK como um arquivo .pem.
* Transfira a chave pública KEK para um computador offline que esteja conectado a um HSM local.
* No computador offline, use a ferramenta BYOK disponibilizada pelo fornecedor do HSM para criar um arquivo BYOK. 
* A chave de destino é criptografada com um KEK, que permanece criptografado até ser transferido para o HSM gerenciado. Apenas a versão criptografada da chave sai do HSM local.
* Um KEK que é gerado dentro de um HSM gerenciado não é exportável. Os HSMs impõem a regra de que não existe nenhuma versão clara de um KEK fora de um HSM gerenciado.
* O KEK deve estar no mesmo HSM gerenciado em que a chave de destino será importada.
* Quando o arquivo BYOK é carregado no HSM gerenciado, um HSM gerenciado usa a chave privada KEK para descriptografar o material de chave de destino e importá-lo como uma chave HSM. Essa operação ocorre inteiramente dentro do HSM. A chave de destino sempre permanece no limite de proteção do HSM.


## <a name="prerequisites"></a>Pré-requisitos

Para usar comandos da CLI do Azure neste artigo, você deve ter os seguintes itens:

* Uma assinatura do Microsoft Azure. Se você não tiver uma, pode se inscrever e fazer uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* A CLI do Azure versão 2.12.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
* Um HSM gerenciado a [lista de HSMs com suporte](#supported-hsms) na sua assinatura. Confira [Início Rápido: Provisione e ative um HSM Gerenciado usando a CLI do Azure](quick-create-cli.md) para provisionar e ativar um HSM Gerenciado.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Para entrar no Azure usando a CLI, você pode digitar:

```azurecli
az login
```

Para saber mais sobre as opções de logon por meio da CLI, veja [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli)

## <a name="supported-hsms"></a>HSMs compatíveis

|Nome do fornecedor|Tipo de fornecedor|Modelos de HSM compatíveis|Mais informações|
|---|---|---|---|
|nCipher|Fabricante,<br/>HSM como serviço|<ul><li>Família de HSMs nShield</li><li>nShield como serviço</ul>|[Nova ferramenta BYOK nCipher e documentação](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|
|Thales|Fabricante|<ul><li>Família HSM 7 da Luna com versão de firmware 7.3 ou mais recente</li></ul>| [Ferramenta BYOK Luna e documentação](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|Fabricante,<br/>HSM como serviço|<ul><li>SDKMS (Serviço de Gerenciamento de Chaves de Proteção Automática)</li><li>Equinix SmartKey</li></ul>|[Como exportar chaves SDKMS para provedores de nuvem para BYOK – Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|
|Marvell|Fabricante|Todos os HSMs LiquidSecurity com<ul><li>Firmware versão 2.0.4 ou posterior</li><li>Firmware versão 3.2 ou mais recente</li></ul>|[Ferramenta BYOK Marvell e documentação](https://www.marvell.com/products/security-solutions/nitrox-hs-adapters/exporting-marvell-hsm-keys-to-cloud-azure-key-vault.html)|
|Cryptomathic|ISV (Sistema de Gerenciamento de Chaves Empresariais)|Várias marcas e modelos de HSM, incluindo<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>Acesse o [site da Cryptomathic para obter detalhes](https://www.cryptomathic.com/azurebyok)|[Ferramenta BYOK Cryptomathic e documentação](https://www.cryptomathic.com/azurebyok)|
|Securosys SA|Fabricante, HSM como serviço|Família HSM Primus, Securosys Clouds HSM|[Ferramenta e documentação do Primus BYOK](https://www.securosys.com/primus-azure-byok)|
|StorMagic|ISV (Sistema de Gerenciamento de Chaves Empresariais)|Várias marcas e modelos de HSM, incluindo<ul><li>Utimaco</li><li>Thales</li><li>nCipher</li></ul>Confira o [site da StorMagic para obter detalhes](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|[SvKMS e Azure Key Vault BYOK](https://stormagic.com/doc/svkms/Content/Integrations/Azure_KeyVault_BYOK.htm)|
|IBM|Fabricante|IBM 476x, CryptoExpress|[IBM Enterprise Key Management Foundation](https://www.ibm.com/security/key-management/ekmf-bring-your-own-key-azure)|
|Utimaco|Fabricante,<br/>HSM como serviço|Âncora u.trust, CryptoServer|[Ferramenta BYOK e Guia de integração da Ultimaco](https://support.hsm.utimaco.com/support/downloads/byok)|
||||


## <a name="supported-key-types"></a>Tipos de chave com suporte

|Nome da chave|Tipo de chave|Tamanho/curva da chave|Origem|Descrição|
|---|---|---|---|---|
|KEK (Chave de Troca de Chaves)|RSA| 2\.048 bits<br />3\.072 bits<br />4\.096 bits|HSM Gerenciado|Um par de chaves RSA com suporte para HSM gerado em HSM gerenciado|
|Chave de destino|
||RSA|2\.048 bits<br />3\.072 bits<br />4\.096 bits|HSM do fornecedor|A chave a ser transferida para o HSM gerenciado|
||EC|P-256<br />P-384<br />P-521|HSM do fornecedor|A chave a ser transferida para o HSM gerenciado|
||Chave simétrica (out-HSM)|128 bits<br />192 bits<br />256 bits|HSM do fornecedor|A chave a ser transferida para o HSM gerenciado|
||||
## <a name="generate-and-transfer-your-key-to-the-managed-hsm"></a>Gerar e transferir sua chave para o HSM gerenciado

Para gerar e transferir sua chave para um HSM gerenciado:

* [Etapa 1: Gerar uma KEK](#step-1-generate-a-kek)
* [Etapa 2: Baixar a chave pública KEK](#step-2-download-the-kek-public-key)
* [Etapa 3: Gerar e preparar sua chave para transferência](#step-3-generate-and-prepare-your-key-for-transfer)
* [Etapa 4: transferir sua chave para HSM gerenciado](#step-4-transfer-your-key-to-managed-hsm)

### <a name="step-1-generate-a-kek"></a>Etapa 1: Gerar uma KEK

Um KEK é uma chave RSA que é gerada em um HSM gerenciado. A KEK é usada para criptografar a chave que você deseja importar (a chave de *destino*).

A KEK precisa ser:
- Uma chave RSA-HSM (2.048 bits, 3.072 bits ou 4.096 bits)
- Gerado no mesmo HSM gerenciado em que você pretende importar a chave de destino
- Criada com operações de chave permitidas definidas como `import`

> [!NOTE]
> A KEK precisa ter 'import' como a única operação de chave permitida. 'import' é mutuamente exclusiva em relação a todas as outras operações de chave.

Use o comando [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create) para criar uma KEK que tenha operações de chave definidas como `import`. Registre o identificador de chave (`kid`) que é retornado do comando a seguir. (Você usará o valor `kid` na [Etapa 3](#step-3-generate-and-prepare-your-key-for-transfer).)

```azurecli-interactive
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --hsm-name ContosoKeyVaultHSM
```
---


### <a name="step-2-download-the-kek-public-key"></a>Etapa 2: Baixar a chave pública KEK

Use [az keyvault key download](/cli/azure/keyvault/key#az-keyvault-key-download) para baixar a chave pública KEK para um arquivo .pem. A chave de destino que você importa é criptografada com a chave pública KEK.

```azurecli-interactive
az keyvault key download --name KEKforBYOK --hsm-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```
---

Transfira o arquivo KEKforBYOK.publickey.pem para o computador offline. Você precisará dele na próxima etapa.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>Etapa 3: Gerar e preparar sua chave para transferência

Veja a documentação do fornecedor do HSM para baixar e instalar a ferramenta BYOK. Siga as instruções do fornecedor do HSM para gerar uma chave de destino e crie um pacote de transferência de chave (um arquivo BYOK). A ferramenta BYOK usará o `kid` da [Etapa 1](#step-1-generate-a-kek) e o arquivo KEKforBYOK.publickey.pem que você baixou na [Etapa 2](#step-2-download-the-kek-public-key) para gerar uma chave de destino criptografada em um arquivo BYOK.

Transfira o arquivo BYOK para o computador conectado.

> [!NOTE] 
> Não há suporte para a importação de chaves RSA de 1.024 bits. Atualmente, não há suporte para a importação de uma chave EC (Curva Elíptica).
>
> **Problema conhecido**: Só há suporte para a importação de uma chave de destino RSA de 4 mil bits de HSMs do Luna no firmware 7.4.0 ou mais recente.

### <a name="step-4-transfer-your-key-to-managed-hsm"></a>Etapa 4: transferir sua chave para HSM gerenciado

Para concluir a importação da chave, transfira o pacote de transferência de chave (um arquivo BYOK) do computador desconectado para o computador conectado à Internet. Use o comando [AZ keyvault Key Import](/cli/azure/keyvault/key#az-keyvault-key-import) para carregar o arquivo BYOK no HSM gerenciado.

```azurecli-interactive
az keyvault key import --hsm-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

Se o upload for bem-sucedido, a CLI do Azure exibirá as propriedades da chave importada.

## <a name="next-steps"></a>Próximas etapas

Agora você pode usar essa chave protegida por HSM em seu HSM gerenciado. Para obter mais informações, confira [esta comparação de preços e recursos](https://azure.microsoft.com/pricing/details/key-vault/).



