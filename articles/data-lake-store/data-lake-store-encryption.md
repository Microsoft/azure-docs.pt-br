---
title: Criptografia no Azure Data Lake Store | Microsoft Docs
description: "Entenda como a rotação de chaves e criptografia funciona no Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: yagupta
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 4/14/2017
ms.author: yagupta
ms.openlocfilehash: 20444d368c568ee716ff242e33323b91ffd198eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="encryption-of-data-in-azure-data-lake-store"></a>Criptografia de dados no Azure Data Lake Store

A criptografia no Azure Data Lake Store ajuda a proteger seus dados, implementar políticas de segurança da empresa e atender aos requisitos de conformidade normativa. Este artigo fornece uma visão geral do design e discute alguns dos aspectos técnicos de implementação.

O Data Lake Store dá suporte à criptografia de dados em repouso e em trânsito. Para dados em repouso, o Data Lake Store oferece suporte à criptografia “ativada por padrão”, transparente. Aqui está o que esses termos significam um pouco mais detalhadamente:

* **Ativada por padrão**: Ao criar uma nova conta do Azure Data Lake Store, a configuração padrão permite a criptografia. Depois disso, os dados armazenados no Data Lake Store são sempre criptografados antes de serem armazenados em mídia persistente. Esse é o comportamento para todos os dados e não pode ser alterado depois que uma conta é criada.
* **Transparente**: O Data Lake Store criptografa automaticamente os dados antes da persistência e descriptografa os dados antes da recuperação. A criptografia é configurada e gerenciada no Data Lake Store por um administrador. Nenhuma alteração é feita para APIs de acesso a dados. Portanto, nenhuma alteração é necessária nos aplicativos e serviços que interagem com o Data Lake Store devido à criptografia.

Dados em trânsito (também conhecidos como dados em movimento) também são sempre criptografados no Data Lake Store. Além de criptografar os dados antes de armazenar em mídia persistente, os dados são sempre protegidos em trânsito usando HTTPS. HTTPS é o único protocolo com suporte para as interfaces REST do Data Lake Store. O diagrama a seguir mostra como os dados serão criptografados no Data Lake Store:

![Diagrama de criptografia de dados no Data Lake Store](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-store"></a>Configurar a criptografia com o Data Lake Store

A criptografia para o Data Lake Store é configurada durante a criação da conta, ela está sempre habilitada por padrão. Você pode gerenciar as chaves por conta própria ou permitir que o Data Lake Store as gerencie para você (esse é o padrão).

Para saber mais, consulte o [Guia de Introdução](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-store"></a>Como funciona a criptografia no Data Lake Store

As informações a seguir abordam como gerenciar chaves de criptografia mestras e explica os três diferentes tipos de chaves que você pode usar na criptografia de dados para Data Lake Store.

### <a name="master-encryption-keys"></a>Chaves de criptografia mestras

O Data Lake Store oferece dois modos de gerenciamento de chaves de criptografia mestras (MEKs). Agora, suponha que a chave de criptografia mestra é a chave de nível superior. O acesso à chave de criptografia mestra é necessário para descriptografar os dados armazenados no Data Lake Store.

Os dois modos para gerenciar a chave de criptografia mestra são da seguinte maneira:

*   Chaves gerenciadas de serviço
*   Chaves gerenciadas do cliente

Em ambos os modos, a chave de criptografia mestra é protegida, armazenando-a no Azure Key Vault. O Key Vault é um serviço altamente seguro, totalmente gerenciado no Azure que pode ser usado para proteger chaves criptográficas. Para obter mais informações, consulte [Key Vault](https://azure.microsoft.com/services/key-vault).

Aqui está uma breve comparação dos recursos fornecidos por dois modos de gerenciar as MEKs.

|  | Chaves gerenciadas de serviço | Chaves gerenciadas do cliente |
| --- | --- | --- |
|Como os dados são armazenados?|Sempre criptografados antes de serem armazenados.|Sempre criptografados antes de serem armazenados.|
|Qual é a Chave de Criptografia Mestra armazenada?|Cofre da Chave|Cofre da Chave|
|Qualquer chave de criptografia é armazenada de modo transparente fora do Key Vault? |Não|Não|
|A MEK pode recuperada pelo Key Vault?|Não. Após a MEK ser armazenada no Key Vault, ela só pode ser usada para criptografia e descriptografia.|Não. Após a MEK ser armazenada no Key Vault, ela só pode ser usada para criptografia e descriptografia.|
|Quem possui a instância e a MEK do Key Vault?|O serviço do Data Lake Store|Você é o proprietário da instância do Key Vault, que pertence à sua própria assinatura do Azure. A MEK no Key Vault pode ser gerenciada pelo software ou hardware.|
|O cliente pode revogar o acesso para a MEK para o serviço do Data Lake Store?|Não|Sim. Você pode gerenciar listas de controle de acesso no Key Vault e remover entradas de controle de acesso para a identidade do serviço para o serviço do Data Lake Store.|
|Você pode excluir permanentemente a MEK?|Não|Sim. Se você excluir a MEK do Key Vault, os dados na conta do Data Lake Store não podem ser descriptografados por ninguém, incluindo o serviço do Data Lake Store. <br><br> Se você fez explicitamente um backup da MEK antes da exclui-la do Key Vault, a MEK pode ser restaurada e os dados podem ser recuperados. No entanto, se você não tiver feito um backup da MEK antes de exclui-la do Key Vault, os dados na conta do Data Lake Store nunca poderão ser descriptografados.|


Além dessa diferença de quem gerencia a MEK e a instância do Key Vault no qual ela reside, o restante do design é o mesmo para ambos os modos.

É importante lembrar do seguinte ao escolher o modo para as chaves de criptografia mestras:

*   Você pode escolher usar chaves gerenciadas pelo cliente ou chaves gerenciadas pelo serviço quando você provisionar uma conta do Data Lake Store.
*   Depois que uma conta do Data Lake Store é configurada, o modo não pode ser alterado.

### <a name="encryption-and-decryption-of-data"></a>Criptografia e descriptografia de dados

Há três tipos de chaves que são usadas no design de criptografia de dados. A tabela a seguir fornece um resumo:

| Chave                   | Abreviação | Associado a | Local de armazenamento                             | Tipo       | Observações                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Chave de criptografia mestra | MEK          | Uma conta Data Lake Store | Cofre da Chave                              | Assimétrica | Pode ser gerenciado pelo Data Lake Store ou por você.                                                              |
| Chave de criptografia de dados   | DEK          | Uma conta Data Lake Store | Armazenamento persistente, gerenciado pelo serviço do Data Lake Store | Simétrica  | A DEK é criptografada pela MEK. A DEK criptografada é a que está armazenada em mídia persistente. |
| Chave de criptografia de bloco  | BEK          | Um bloco de dados | Nenhum                                         | Simétrica  | A BEK é derivada da DEK e do bloco de dados.                                                      |

O diagrama a seguir ilustra esses conceitos:

![Chaves na criptografia de dados](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudoalgoritmo quando um arquivo será descriptografado:
1.  Verifique se a DEK para a conta do Data Lake Store está armazenada em cache e pronta para uso.
    - Caso contrário, leia a DEK criptografada de armazenamento persistente e envie-a para o Key Vault para ser descriptografada. Armazene em cache a DEK descriptografada na memória. Agora ela está pronta para ser usada.
2.  Para cada bloco de dados no arquivo:
    - Leia o bloco de dados criptografado do armazenamento persistente.
    - Gere a BEK a partir da DEK e do bloco de dados criptografado.
    - Use a BEK para descriptografar dados.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudoalgoritmo quando um bloco de dados será descriptografado:
1.  Verifique se a DEK para a conta do Data Lake Store está armazenada em cache e pronta para uso.
    - Caso contrário, leia a DEK criptografada de armazenamento persistente e envie-a para o Key Vault para ser descriptografada. Armazene em cache a DEK descriptografada na memória. Agora ela está pronta para ser usada.
2.  Gere um BEK exclusivo para o bloco de dados do DEK.
3.  Criptografe o bloco de dados com a BEK usando a criptografia AES-256.
4.  Armazene o bloco de dados criptografados no armazenamento persistente.

> [!NOTE] 
> Para fins de desempenho, a DEK criptografada é armazenada em cache na memória por um curto período e é imediatamente apagada posteriormente. Na mídia persistente, ela é sempre armazenada criptografada pela MEK.

## <a name="key-rotation"></a>Alteração de chaves

Quando você estiver usando chaves gerenciados pelo cliente, você pode alterar a MEK. Para saber como configurar uma conta do Data Lake Store com chaves gerenciadas pelo cliente, consulte a [Introdução](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Pré-requisitos

Quando você configurou a conta de armazenamento do Data Lake Store, você optou por usar suas próprias chaves. Essa opção não pode ser alterada depois que a conta foi criada. As etapas a seguir pressupõem que você está usando as chaves gerenciadas pelo cliente (isto é, você escolheu suas próprias chaves do Key Vault).

Observe que, se você usar as opções padrão para criptografia, seus dados sempre são criptografados usando chaves gerenciadas pelo Data Lake Store. Nessa opção, você não tem a capacidade de alterar as chaves, pois elas são gerenciadas pelo Data Lake Store.

### <a name="how-to-rotate-the-mek-in-data-lake-store"></a>Como alterar a MEK no Data Lake Store

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Navegue até o Key Vault que armazena as chaves associadas à sua conta do Data Lake Store. Selecione as **Chaves**.

    ![Captura de tela do Key Vault](./media/data-lake-store-encryption/keyvault.png)

3.  Selecione a chave associada à sua conta do Azure Data Lake Store e crie uma nova versão dessa chave. Observe que Data Lake Store atualmente suporta a alteração de chaves apenas para uma nova versão de uma chave. Ele não oferece suporte a alteração para uma chave diferente.

   ![Captura de tela da janela de Chaves, com a Nova versão realçada](./media/data-lake-store-encryption/keynewversion.png)

4.  Navegue até a conta de armazenamento do Data Lake Store e selecione **Criptografia**.

    ![Captura de tela da janela da conta de armazenamento do Data Lake Store, com Criptografia realçada](./media/data-lake-store-encryption/select-encryption.png)

5.  Uma mensagem notifica você que uma nova versão de chave da chave está disponível. Clique em **Alterar chave** para atualizar a chave para a nova versão.

    ![Captura de tela da janela do Data Lake Store com a mensagem e Alterar realçados](./media/data-lake-store-encryption/rotatekey.png)

Esta operação deve levar menos de dois minutos e não há nenhum tempo de inatividade esperado devido à alteração de chaves. Após a operação ser concluída, a nova versão da chave estará em uso.
