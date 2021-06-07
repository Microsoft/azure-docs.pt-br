---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: c3a27218f03980e9b42a4a56fa739203c43754a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013204"
---
<!--Set the encryption on storage account in the portal-->

## <a name="set-the-encryption-on-a-storage-account"></a>Definir a criptografia em uma conta de armazenamento

1. No portal do Azure, insira o nome da conta de armazenamento que deseja criptografar no campo **Pesquisar** na parte superior da tela.  As correspondências serão exibidas abaixo do campo de pesquisa.
1. Selecione a conta de armazenamento que você está procurando. A tela da conta de armazenamento será exibida.
1. Selecione **Criptografia**.
1. Selecione Chaves gerenciadas da Microsoft ou Chaves gerenciadas pelo cliente.

### <a name="use-microsoft-managed-keys"></a>Usar as chaves gerenciadas pela Microsoft

Por padrão, os dados na conta de armazenamento são criptografados por meio das chaves gerenciadas pela Microsoft.

### <a name="use-customer-managed-keys"></a>Usar chaves gerenciadas pelo cliente

1. Selecione **Chaves gerenciadas pelo cliente**.
1. Selecione **Inserir o URI de chave** ou **Selecionar no cofre de chaves**.
    1. Se você selecionar **Inserir URI de chave**, insira o URI de chave no campo URI de chave e selecione a assinatura. (Ele já pode estar selecionado para você.)
    1. Se você selecionar **Selecionar do cofre de chaves**, escolherá **Selecionar um cofre de chaves e uma chave**. A tela Selecionar chave do Azure Key Vault será exibida.
1. Escolha o **Key Vault** que deseja usar e selecione uma chave que você já tem no seu cofre de chaves ou **crie uma chave**.
    1. Se você optar por criar uma chave, selecione **Gerar** ou **Importar** na lista suspensa **Opções**. Você só pode importar chaves RSA.
    1. Para gerar uma nova chave, dê um nome à chave no campo **Nome** e selecione o Tipo de chave:
        1. Tamanho de Chaves – RSA:  2.048, 3.072 ou 4.096. Isso é o que a maioria dos clientes escolhe.
        1. Nomes de Curva Elíptica – EC: P-256, P-384, P-521 ou P-256K
        1. Opcionalmente, você pode definir as datas de ativação e validade da chave.
        1. Escolha **Sim** para habilitar a rotação automática de chaves.
        1. Selecione **Criar**.
    1. Para importar uma chave, selecione o arquivo a ser carregado clicando em qualquer lugar no **campo Selecionar um arquivo**.
        1. Dê um nome à chave no campo **Nome**.
        1. Opcionalmente, você pode definir as datas de ativação e validade da chave.
        1. Escolha **Sim** para habilitar a rotação automática de chaves.
        1. Selecione **Criar**.
    1. Escolha **Selecionar** para selecionar essa chave para criptografar sua conta de armazenamento. Você será levado de volta à tela Criptografia.
1. **IMPORTANTE:** Selecione **Salvar** para salvar as configurações de criptografia ou tudo o que você acabou de fazer será perdido.
