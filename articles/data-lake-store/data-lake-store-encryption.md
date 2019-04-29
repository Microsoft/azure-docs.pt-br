---
title: Criptografia no Azure Data Lake Storage Gen1 | Microsoft Docs
description: A criptografia no Azure Data Lake Storage Gen1 ajuda a proteger os dados, implementar políticas de segurança corporativa e atender aos requisitos de conformidade normativa. Este artigo fornece uma visão geral do design e discute alguns dos aspectos técnicos de implementação.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: a009f212bd8baaa353d602dc6090aeeccddd4936
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60878347"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Criptografia de dados no Azure Data Lake Storage Gen1

A criptografia no Azure Data Lake Storage Gen1 ajuda a proteger os dados, implementar políticas de segurança corporativa e atender aos requisitos de conformidade normativa. Este artigo fornece uma visão geral do design e discute alguns dos aspectos técnicos de implementação.

O Data Lake Storage Gen1 dá suporte a criptografia de dados em repouso e em trânsito. Para dados em repouso, o Data Lake Storage Gen1 dá suporte a criptografia transparente "ativada por padrão". Aqui está o que esses termos significam um pouco mais detalhadamente:

* **Em por padrão**: Quando você cria uma nova conta do Data Lake armazenamento Gen1, a configuração padrão permite a criptografia. Posteriormente, os dados armazenados no Data Lake Storage Gen1 são sempre criptografados antes de serem armazenados em mídia persistente. Esse é o comportamento para todos os dados e não pode ser alterado depois que uma conta é criada.
* **Transparente**: Data Lake armazenamento Gen1 automaticamente criptografa os dados antes da persistência e descriptografa os dados antes da recuperação. A criptografia é configurada e gerenciada no nível de conta do Data Lake Storage Gen1 por um administrador. Nenhuma alteração é feita para APIs de acesso a dados. Portanto, nenhuma alteração é necessária em aplicativos e serviços que interagem com o Data Lake Storage Gen1 devido à criptografia.

Os dados em trânsito (também conhecidos como dados em movimento) também são sempre criptografados no Data Lake Storage Gen1. Além de criptografar os dados antes de armazenar em mídia persistente, os dados são sempre protegidos em trânsito usando HTTPS. HTTPS é o único protocolo com suporte para as interfaces REST do Data Lake Storage Gen1. O diagrama a seguir mostra como os dados são criptografados no Data Lake Storage Gen1:

![Diagrama de criptografia de dados no Data Lake Storage Gen1](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Configurar criptografia com Data Lake Storage Gen1

A criptografia para Data Lake Storage Gen1 é configurada durante a criação da conta e é sempre ativada por padrão. Você pode gerenciar as chaves por conta própria ou permitir que o Data Lake Storage Gen1 as gerencie para você (esse é o padrão).

Para saber mais, consulte o [Guia de Introdução](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Como a criptografia funciona no Data Lake Storage Gen1

As informações a seguir abordam como gerenciar chaves de criptografia mestras e explica os três diferentes tipos de chaves que você pode usar na criptografia de dados para Data Lake Storage Gen1.

### <a name="master-encryption-keys"></a>Chaves de criptografia mestras

O Data Lake Storage Gen1 fornece dois modos para gerenciamento de MEKs (chaves de criptografia mestras). Agora, suponha que a chave de criptografia mestra é a chave de nível superior. O acesso à chave de criptografia mestra é necessário para descriptografar os dados armazenados no Data Lake Storage Gen1.

Os dois modos para gerenciar a chave de criptografia mestra são da seguinte maneira:

*   Chaves gerenciadas de serviço
*   Chaves gerenciadas do cliente

Em ambos os modos, a chave de criptografia mestra é protegida, armazenando-a no Azure Key Vault. O Key Vault é um serviço altamente seguro, totalmente gerenciado no Azure que pode ser usado para proteger chaves criptográficas. Para obter mais informações, consulte [Key Vault](https://azure.microsoft.com/services/key-vault).

Aqui está uma breve comparação dos recursos fornecidos por dois modos de gerenciar as MEKs.

|  | Chaves gerenciadas de serviço | Chaves gerenciadas do cliente |
| --- | --- | --- |
|Como os dados são armazenados?|Sempre criptografados antes de serem armazenados.|Sempre criptografados antes de serem armazenados.|
|Qual é a Chave de Criptografia Mestra armazenada?|Key Vault|Key Vault|
|Qualquer chave de criptografia é armazenada de modo transparente fora do Key Vault? |Não |Não |
|A MEK pode recuperada pelo Key Vault?|Não. Após a MEK ser armazenada no Key Vault, ela só pode ser usada para criptografia e descriptografia.|Não. Após a MEK ser armazenada no Key Vault, ela só pode ser usada para criptografia e descriptografia.|
|Quem possui a instância e a MEK do Key Vault?|O serviço Azure Data Lake Storage Gen1|Você é o proprietário da instância do Key Vault, que pertence à sua própria assinatura do Azure. A MEK no Key Vault pode ser gerenciada pelo software ou hardware.|
|É possível revogar o acesso ao MEK para o serviço Data Lake Storage Gen1?|Não |Sim. É possível gerenciar listas de controle de acesso no Key Vault e remover entradas de controle de acesso à identidade de serviço do serviço Data Lake Storage Gen1.|
|Você pode excluir permanentemente a MEK?|Não |Sim. Se você excluir a MEK do Key Vault, os dados da conta Data Lake Storage Gen1 não poderão ser descriptografados por ninguém, incluindo o serviço Data Lake Storage Gen1. <br><br> Se você fez explicitamente um backup da MEK antes da exclui-la do Key Vault, a MEK pode ser restaurada e os dados podem ser recuperados. No entanto, se você não tiver feito o backup da MEK antes de excluí-la do Key Vault, os dados da conta Data Lake Storage Gen1 nunca poderão ser descriptografados depois disso.|


Além dessa diferença de quem gerencia a MEK e a instância do Key Vault no qual ela reside, o restante do design é o mesmo para ambos os modos.

É importante lembrar do seguinte ao escolher o modo para as chaves de criptografia mestras:

*   Você pode optar por usar as chaves gerenciadas pelo cliente ou as chaves gerenciadas pelo serviço ao provisionar uma conta do Data Lake Storage Gen1.
*   Depois que uma conta do Data Lake Storage Gen1 for provisionada, o modo não poderá ser alterado.

### <a name="encryption-and-decryption-of-data"></a>Criptografia e descriptografia de dados

Há três tipos de chaves que são usadas no design de criptografia de dados. A tabela a seguir fornece um resumo:

| Chave                   | Abreviação | Associado a | Local de armazenamento                             | Type       | Observações                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Chave de criptografia mestra | MEK          | Uma conta do Data Lake Storage Gen1 | Key Vault                              | Assimétrica | Pode ser gerenciado pelo Data Lake Storage Gen1 ou por você.                                                              |
| Chave de criptografia de dados   | DEK          | Uma conta do Data Lake Storage Gen1 | Armazenamento persistente, gerenciado pelo serviço Data Lake Storage Gen1 | Simétrica  | A DEK é criptografada pela MEK. A DEK criptografada é a que está armazenada em mídia persistente. |
| Chave de criptografia de bloco  | BEK          | Um bloco de dados | Nenhum                                         | Simétrica  | A BEK é derivada da DEK e do bloco de dados.                                                      |

O diagrama a seguir ilustra esses conceitos:

![Chaves na criptografia de dados](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudoalgoritmo quando um arquivo será descriptografado:
1.  Verifique se a DEK da conta Data Lake Storage Gen1 está em cache e pronta para uso.
    - Caso contrário, leia a DEK criptografada de armazenamento persistente e envie-a para o Key Vault para ser descriptografada. Armazene em cache a DEK descriptografada na memória. Agora ela está pronta para ser usada.
2.  Para cada bloco de dados no arquivo:
    - Leia o bloco de dados criptografado do armazenamento persistente.
    - Gere a BEK a partir da DEK e do bloco de dados criptografado.
    - Use a BEK para descriptografar dados.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudoalgoritmo quando um bloco de dados será descriptografado:
1.  Verifique se a DEK da conta Data Lake Storage Gen1 está em cache e pronta para uso.
    - Caso contrário, leia a DEK criptografada de armazenamento persistente e envie-a para o Key Vault para ser descriptografada. Armazene em cache a DEK descriptografada na memória. Agora ela está pronta para ser usada.
2.  Gere um BEK exclusivo para o bloco de dados do DEK.
3.  Criptografe o bloco de dados com a BEK usando a criptografia AES-256.
4.  Armazene o bloco de dados criptografados no armazenamento persistente.

> [!NOTE] 
> A DEK sempre é armazenada criptografada pela MEK, seja na mídia persistente ou em cache na memória.

## <a name="key-rotation"></a>Alteração de chaves

Quando você estiver usando chaves gerenciados pelo cliente, você pode alterar a MEK. Para saber como configurar uma conta do Data Lake Storage Gen1 com chaves gerenciadas pelo cliente, consulte [Introdução](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

### <a name="prerequisites"></a>Pré-requisitos

Ao configurar a conta do Data Lake Storage Gen1, você optou por usar suas próprias chaves. Essa opção não pode ser alterada depois que a conta foi criada. As etapas a seguir pressupõem que você está usando as chaves gerenciadas pelo cliente (isto é, você escolheu suas próprias chaves do Key Vault).

Observe que, se você usar as opções padrão para criptografia, seus dados serão sempre criptografados usando chaves gerenciadas pelo Data Lake Storage Gen1. Nessa opção, você não pode girar as chaves, pois elas são gerenciadas pelo Data Lake Storage Gen1.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>Como girar a MEK no Data Lake Storage Gen1

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Navegue até a instância do Key Vault que armazena suas chaves associadas à sua conta do Data Lake Storage Gen1. Selecione **Chaves**.

    ![Captura de tela do Key Vault](./media/data-lake-store-encryption/keyvault.png)

3. Selecione a chave associada à sua conta do Data Lake Storage Gen1 e crie uma nova versão dessa chave. Observe que atualmente o Data Lake Storage Gen1 dá suporte apenas para rotação de chave para uma nova versão de uma chave. Ele não oferece suporte a alteração para uma chave diferente.

   ![Captura de tela da janela de Chaves, com a Nova versão realçada](./media/data-lake-store-encryption/keynewversion.png)

4. Navegue até a conta do Data Lake Storage Gen1 e selecione **Criptografia**.

   ![Captura de tela da janela da conta do Data Lake Storage Gen1, com Criptografia destacada](./media/data-lake-store-encryption/select-encryption.png)

5. Uma mensagem notifica você que uma nova versão de chave da chave está disponível. Clique em **Alterar chave** para atualizar a chave para a nova versão.

   ![Captura de tela da janela do Data Lake Storage Gen1 com mensagem e Chave de Rotação destacada](./media/data-lake-store-encryption/rotatekey.png)

Esta operação deve levar menos de dois minutos e não há nenhum tempo de inatividade esperado devido à alteração de chaves. Após a operação ser concluída, a nova versão da chave estará em uso.

> [!IMPORTANT]
> Após a conclusão da operação de rotação de chaves, a versão antiga da chave não é mais ativamente é usada para criptografar os dados.  No entanto, em casos raros de falha inesperada, em que até mesmo as cópias redundantes de seus dados são afetadas, os dados poderão ser restaurados de um backup que ainda está usando a chave antiga. Para garantir a acessibilidade de seus dados nessas circunstâncias raras, mantenha uma cópia da versão anterior da sua chave de criptografia. Consulte [Diretrizes para recuperação de desastre para dados no Data Lake Storage Gen1](data-lake-store-disaster-recovery-guidance.md) para obter melhores práticas do planejamento de recuperação de desastre. 