---
title: Usar chaves gerenciadas pelo cliente para criptografar dados no cache HPC do Azure
description: Como usar Azure Key Vault com o cache do Azure HPC para controlar o acesso à chave de criptografia em vez de usar as chaves de criptografia padrão gerenciadas pela Microsoft
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/20/2020
ms.author: v-erkel
ms.openlocfilehash: f587de4ee2ce051cb771db90d7f9ce00ce66b07f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772698"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Usar chaves de criptografia gerenciadas pelo cliente para o cache do Azure HPC

Você pode usar Azure Key Vault para controlar a propriedade das chaves usadas para criptografar seus dados no cache do HPC do Azure. Este artigo explica como usar chaves gerenciadas pelo cliente para criptografia de dados de cache.

> [!NOTE]
> Todos os dados armazenados no Azure, incluindo os discos de cache, são criptografados em repouso usando chaves gerenciadas pela Microsoft por padrão. Você só precisa seguir as etapas neste artigo se quiser gerenciar as chaves usadas para criptografar seus dados.

O cache HPC do Azure também é protegido pela [criptografia do host da VM](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data) nos discos gerenciados que mantêm os dados armazenados em cache, mesmo que você adicione uma chave do cliente aos discos de cache. Adicionar uma chave gerenciada pelo cliente para criptografia dupla fornece um nível extra de segurança para clientes com necessidades de alta segurança. Leia [criptografia no servidor do armazenamento em disco do Azure](../virtual-machines/disk-encryption.md) para obter detalhes.

Há três etapas para habilitar a criptografia de chave gerenciada pelo cliente para o cache do Azure HPC:

1. Configure um Azure Key Vault para armazenar as chaves.
1. Ao criar o cache HPC do Azure, escolha criptografia de chave gerenciada pelo cliente e especifique o cofre de chaves e a chave a serem usados.
1. Depois que o cache for criado, autorize-o a acessar o cofre de chaves.

A criptografia não é totalmente configurada até que você a autorize do cache recém-criado (etapa 3). Isso ocorre porque você deve passar a identidade do cache para o cofre de chaves para torná-lo um usuário autorizado. Você não pode fazer isso antes de criar o cache, porque a identidade não existe até que o cache seja criado.

Depois de criar o cache, você não pode alterar entre chaves gerenciadas pelo cliente e chaves gerenciadas pela Microsoft. No entanto, se o cache usar chaves gerenciadas pelo cliente, você poderá [alterar](#update-key-settings) a chave de criptografia, a versão da chave e o cofre de chaves conforme necessário.

## <a name="understand-key-vault-and-key-requirements"></a>Entender o Key Vault e os principais requisitos

O cofre de chaves e a chave devem atender a esses requisitos para trabalhar com o cache do HPC do Azure.

Propriedades do Key Vault:

* **Assinatura** – use a mesma assinatura que é usada para o cache.
* **Região** -o cofre de chaves deve estar na mesma região que o cache do HPC do Azure.
* **Tipo de preço** – a camada Standard é suficiente para uso com o cache do HPC do Azure.
* **Exclusão reversível** – o cache HPC do Azure habilitará a exclusão reversível se ela ainda não estiver configurada no cofre de chaves.
* **Limpar proteção** – a proteção de limpeza deve ser habilitada.
* **Política de acesso** -as configurações padrão são suficientes.
* **Conectividade de rede** – o cache HPC do Azure deve ser capaz de acessar o cofre de chaves, independentemente das configurações de ponto de extremidade que você escolher.

Propriedades da chave:

* **Tipo de chave** -RSA
* **Tamanho da chave RSA** -2048
* **Habilitado** -Sim

Permissões de acesso do cofre de chaves:

* O usuário que cria o cache HPC do Azure deve ter permissões equivalentes à [função colaborador de Key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor). As mesmas permissões são necessárias para configurar e gerenciar Azure Key Vault.

  Leia [acesso seguro a um cofre de chaves](../key-vault/general/secure-your-key-vault.md) para obter mais informações.

## <a name="1-set-up-azure-key-vault"></a>1. configurar Azure Key Vault

Você pode configurar um cofre de chaves e uma chave antes de criar o cache ou fazer isso como parte da criação do cache. Verifique se esses recursos atendem aos requisitos descritos [acima](#understand-key-vault-and-key-requirements).

No momento da criação do cache, você deve especificar um cofre, uma chave e uma versão de chave para usar na criptografia do cache.

Leia a [documentação do Azure Key Vault](../key-vault/general/overview.md) para obter detalhes.

> [!NOTE]
> O Azure Key Vault deve usar a mesma assinatura e estar na mesma região que o cache do HPC do Azure. Verifique se a região escolhida [dá suporte a ambos os produtos](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault).

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. criar o cache com chaves gerenciadas pelo cliente habilitadas

Você deve especificar a origem da chave de criptografia ao criar o cache do HPC do Azure. Siga as instruções em [criar um cache do HPC do Azure](hpc-cache-create.md)e especifique o cofre de chaves e a chave na página **chaves de criptografia de disco** . Você pode criar um novo cofre de chaves e uma chave durante a criação do cache.

> [!TIP]
> Se a página **chaves de criptografia de disco** não for exibida, verifique se o cache está em uma das [regiões com suporte](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=hpc-cache,key-vault).

O usuário que cria o cache deve ter privilégios iguais à [função de colaborador de Key Vault](../role-based-access-control/built-in-roles.md#key-vault-contributor) ou superior.

1. Clique no botão para habilitar chaves gerenciadas de modo privado. Depois de alterar essa configuração, as configurações do cofre de chaves são exibidas.

1. Clique em **selecionar um cofre de chaves** para abrir a página seleção de chave. Escolha ou crie o cofre de chaves e a chave para criptografar dados nos discos desse cache.

   Se o Azure Key Vault não aparecer na lista, verifique estes requisitos:

   * O cache está na mesma assinatura que o cofre de chaves?
   * O cache está na mesma região que o cofre de chaves?
   * Há conectividade de rede entre o portal do Azure e o cofre de chaves?

1. Depois de selecionar um cofre, selecione a chave individual nas opções disponíveis ou crie uma nova chave. A chave deve ser uma chave RSA de 2048 bits.

1. Especifique a versão para a chave selecionada. Saiba mais sobre o controle de versão na [documentação do Azure Key Vault](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning).

Continue com o restante das especificações e crie o cache conforme descrito em [criar um cache do HPC do Azure](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. autorizar Azure Key Vault criptografia do cache
<!-- header is linked from create article, update if changed -->

Após alguns minutos, o novo cache do Azure HPC aparecerá em seu portal do Azure. Vá para a página de **visão geral** para autorizá-lo a acessar seu Azure Key Vault e habilitar a criptografia de chave gerenciada pelo cliente.

> [!TIP]
> O cache pode aparecer na lista de recursos antes que as mensagens de "implantação em andamento" sejam limpas. Verifique sua lista de recursos após um ou dois minutos em vez de aguardar uma notificação de êxito.

Esse processo de duas etapas é necessário porque a instância de cache do HPC do Azure precisa de uma identidade para passar para a Azure Key Vault para autorização. A identidade do cache não existe até que suas etapas de criação iniciais sejam concluídas.

> [!NOTE]
> Você deve autorizar a criptografia dentro de 90 minutos depois de criar o cache. Se você não concluir esta etapa, o cache atingirá o tempo limite e falhará. Um cache com falha deve ser recriado; ele não pode ser corrigido.

O cache mostra o status **aguardando a chave**. Clique no botão **habilitar criptografia** na parte superior da página para autorizar o cache a acessar o cofre de chaves especificado.

![captura de tela da página Visão geral do cache no portal, com realce no botão habilitar criptografia (linha superior) e status: aguardando chave](media/waiting-for-key.png)

Clique em **habilitar criptografia** e, em seguida, clique no botão **Sim** para autorizar o cache a usar a chave de criptografia. Essa ação também habilita a exclusão reversível e a proteção de limpeza (se ainda não estiver habilitada) no cofre de chaves.

![captura de tela da página Visão geral do cache no portal, com uma mensagem de faixa na parte superior que solicita que o usuário habilite a criptografia clicando em Sim](media/enable-keyvault.png)

Depois que o cache solicita acesso ao cofre de chaves, ele pode criar e criptografar os discos que armazenam dados armazenados em cache.

Depois de autorizar a criptografia, o cache HPC do Azure passa por vários minutos de instalação para criar os discos criptografados e a infraestrutura relacionada.

## <a name="update-key-settings"></a>Atualizar configurações de chave

Você pode alterar o cofre de chaves, a chave ou a versão de chave do seu cache do portal do Azure. Clique no link configurações de **criptografia** do cache para abrir a página **configurações de chave do cliente** .

Você não pode alterar um cache entre chaves gerenciadas pelo cliente e chaves gerenciadas pelo sistema.

![captura de tela da página "configurações de chaves do cliente", acessada clicando em configurações > criptografia na página cache na portal do Azure](media/change-key-click.png)

Clique no link **alterar chave** , em seguida, clique em **alterar o cofre de chaves, a chave ou a versão** para abrir o seletor de chave.

![captura de tela da página "selecionar chave de Azure Key Vault" com três seletores suspensos para escolher o cofre de chaves, a chave e a versão](media/select-new-key.png)

Os cofres de chaves na mesma assinatura e na mesma região que esse cache são mostrados na lista.

Depois de escolher os novos valores de chave de criptografia, clique em **selecionar**. Uma página de confirmação é exibida com os novos valores. Clique em **salvar** para finalizar a seleção.

![captura de tela da página de confirmação com o botão salvar na parte superior esquerda](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Leia mais sobre chaves gerenciadas pelo cliente no Azure

Estes artigos explicam mais sobre como usar Azure Key Vault e chaves gerenciadas pelo cliente para criptografar dados no Azure:

* [Visão geral da criptografia de armazenamento do Azure](../storage/common/storage-service-encryption.md)
* [Criptografia de disco com chaves gerenciadas pelo cliente](../virtual-machines/disk-encryption.md#customer-managed-keys) -documentação para usar Azure Key Vault com discos gerenciados, que é um cenário semelhante ao cache HPC do Azure

## <a name="next-steps"></a>Próximas etapas

Depois de criar o cache do HPC do Azure e a criptografia baseada em Key Vault autorizada, continue a configurar seu cache, concedendo-lhe acesso às suas fontes de dados.

* [Adicionar destinos de armazenamento](hpc-cache-add-storage.md)
