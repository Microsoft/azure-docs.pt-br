---
title: Criptografar discos em uma VM Linux com a CLI 1.0 do Azure | Microsoft Docs
description: "Como criptografar discos em uma VM Linux usando a CLI 1.0 do Azure e o modelo de implantação do Resource Manager"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2017
ms.author: iainfou
ms.openlocfilehash: b436f2d43c41000f4385889edb3fa3983d4a8c66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="encrypt-disks-on-a-linux-vm-using-the-azure-cli-10"></a>Criptografar discos em uma VM Linux usando a CLI 1.0 do Azure
Para conformidade e segurança aprimorados da máquina virtual (VM), os discos virtuais no Azure podem ser criptografados em repouso. Discos são criptografados usando chaves criptográficas que são protegidas em um Cofre de chaves do Azure. Você controla essas chaves criptográficas e pode auditar seu uso. Este artigo fornece detalhes sobre como criptografar discos virtuais em uma VM Linux usando a CLI 1.0 do Azure e o modelo de implantação do Resource Manager.

## <a name="cli-versions-to-complete-the-task"></a>Versões da CLI para concluir a tarefa
Você pode concluir a tarefa usando uma das seguintes versões da CLI:

- [CLI 1.0 do Azure](#quick-commands) – nossa CLI para os modelos de implantação clássico e de gerenciamento de recursos (este artigo)
- [CLI 2.0 do Azure](encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – nossa última geração de CLI para o modelo de implantação de gerenciamento de recursos

## <a name="quick-commands"></a>Comandos rápidos
Se você precisar realizar rapidamente a tarefa, a seção a seguir detalha a base de dados de comandos para criptografar discos virtuais em sua VM. Mais informações detalhadas e contexto para cada etapa podem ser encontrados no restante do documento, [começando aqui](#overview-of-disk-encryption).

É necessário que a [CLI 1.0 do Azure mais recente](../../xplat-cli-install.md) esteja instalada e conectada usando o modo do Resource Manager da seguinte maneira:

```azurecli
azure config mode arm
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluem `myResourceGroup`, `myKeyVault` e `myVM`.

Primeiro, habilite o provedor do Cofre de chaves do Azure dentro de sua assinatura do Azure e crie um grupo de recursos. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` na localização `WestUS`:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

Crie um Cofre de chaves do Azure. O exemplo a seguir cria um Cofre de Chaves chamado `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

Crie uma chave de criptografia em seu Cofre de chaves e habilite-a para criptografia de disco. O exemplo a seguir cria uma chave chamada `myKey`:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```

Crie um ponto de extremidade usando o Azure Active Directory para manipular a autenticação e a troca de chaves criptográficas do Cofre de chaves. O `--home-page` e `--identifier-uris` não precisa ser um endereço roteável real. No nível mais alto de segurança, os segredos de cliente devem ser usados em vez de senhas. Atualmente, a CLI do Azure não pode gerar segredos de cliente. Segredos do cliente só podem ser gerados no portal do Azure. O exemplo a seguir cria um ponto de extremidade do Azure Active Directory chamado `myAADApp` e usa uma senha de `myPassword`. Especifique sua própria senha da seguinte maneira:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Observe o `applicationId` mostrado na saída do comando anterior. Essa ID de aplicativo é usada nas seguintes etapas:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```

Adicione um disco de dados a uma VM existente. O exemplo a seguir adiciona um disco de dados a uma VM denominada `myVM`:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Examine os detalhes de seu Cofre de chaves e a chave que você criou. Na etapa final, você precisará da ID do Cofre de chaves, do URI e do URL da chave. O exemplo a seguir examina os detalhes para um Cofre de chaves chamado `myKeyVault` e para a chave chamada `myKey`:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Criptografe seus discos da seguinte maneira, digitando seus próprios nomes de parâmetro completamente:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

A CLI do Azure não fornece erros detalhados durante o processo de criptografia. Para obter informações adicionais de solução de problemas, examine `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log`. Como o comando anterior tem muitas variáveis e você não recebe indicação suficiente sobre como o processo falha, um exemplo de comando completo seria o seguinte:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

Finalmente, examine o status de criptografia novamente para confirmar se os discos virtuais agora estão criptografados. O exemplo a seguir verifica o status de uma VM denominada `myVM` no grupo de recursos `myResourceGroup`:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```

## <a name="overview-of-disk-encryption"></a>Visão geral da criptografia de disco
Discos virtuais em VMs do Linux são criptografados em repouso usando [dm-crypt](https://wikipedia.org/wiki/Dm-crypt). Não há nenhuma taxa para criptografar discos virtuais no Azure. Chaves e criptográficas são armazenadas no Cofre de chaves do Azure usando a proteção de software ou você pode importar ou gerar as chaves em Módulos de segurança de Hardware (HSMs) certificados para padrões de nível 2 de FIPS 140-2. Você mantém o controle dessas chaves criptográficas e pode auditar seu uso. Essas chaves criptográficas são usadas para criptografar e descriptografar os discos virtuais conectados à sua VM. Um ponto de extremidade do Azure Active Directory fornece um mecanismo seguro para emitir essas chaves criptográficas, enquanto as VMs são ligadas e desligadas.

O processo de criptografia de uma VM é o seguinte:

1. Crie uma chave de criptografia em um Cofre de chaves do Azure.
2. Configure a chave de criptografia a ser usada para criptografar discos.
3. Para ler a chave de criptografia do Cofre de chaves do Azure, crie um ponto de extremidade usando o Azure Active Directory com as permissões apropriadas.
4. Execute o comando para criptografar seus discos virtuais, especificando o ponto de extremidade do Azure Active Directory e a chave de criptografia apropriada a ser usada.
5. O ponto de extremidade do Azure Active Directory solicita a chave de criptografia necessária do Cofre de chaves do Azure.
6. Os discos virtuais são criptografados usando a chave de criptografia fornecida.

## <a name="supporting-services-and-encryption-process"></a>Suporte a serviços e processo de criptografia
A criptografia de disco depende dos seguintes componentes adicionais:

* **Cofre de Chaves do Azure** – usado para proteger chaves criptográficas e segredos usados para o processo de criptografia/descriptografia do disco.
  * Se houver um, você pode usar um Cofre de Chaves do Azure existente. Não é necessário dedicar um Cofre de Chaves para criptografar discos.
  * Para separar os limites administrativos e visibilidade de chave, crie um Cofre de Chaves dedicado.
* **Azure Active Directory** – realiza a troca segura de chaves criptográficas necessárias e a autenticação para ações solicitadas.
  * Normalmente, você pode usar uma instância existente do Azure Active Directory para hospedar seu aplicativo.
  * O aplicativo é mais um ponto de extremidade para os serviços de Máquina Virtual e Cofre de chaves para solicitar e receber as chaves criptográficas apropriadas. Você não está desenvolvendo um aplicativo real que se integra ao Azure Active Directory.

## <a name="requirements-and-limitations"></a>Requisitos e limitações
Requisitos e cenários com suporte para criptografia de disco:

* Os seguintes SKUs de servidor Linux – Ubuntu, CentOS, SUSE e SLES (SUSE Linux Enterprise Server) e Red Hat Enterprise Linux.
* Todos os recursos (por exemplo, Cofre de chaves, conta de Armazenamento e VM) devem pertencer à mesma assinatura e região do Azure.
* VMs Standard das séries A, D, DS, G e GS.

A criptografia de disco não tem suporte atualmente nos seguintes cenários:

* VMs de camada básica.
* VMs criadas com o modelo de implantação Clássico.
* Desabilitação da criptografia de disco do OS em VMs do Linux.
* Atualização das chaves de criptografia em uma VM Linux já está criptografado.

## <a name="create-the-azure-key-vault-and-keys"></a>Criar o Cofre de Chaves do Azure e chaves
Para concluir o restante deste guia, é necessário que a [CLI 1.0 do Azure mais recente](../../xplat-cli-install.md) esteja instalada e conectada usando o modo do Resource Manager da seguinte maneira:

```azurecli
azure config mode arm
```

Em todos os exemplos de comando, substitua todos os parâmetros de exemplo pelos seus próprios valores de nome, localização e chave. Os seguintes exemplos usam uma convenção de `myResourceGroup`, `myKeyVault`, `myAADApp` etc.

A primeira etapa é criar um Cofre de Chaves do Azure para armazenar as chaves criptográficas. O Cofre de Chaves do Azure pode armazenar chaves, segredos ou senhas que permitem implementá-los de forma segura em seus aplicativos e serviços. Para criptografia de disco virtual, use o Cofre de chaves para armazenar uma chave de criptografia que é usada para criptografar ou descriptografar seus discos virtuais.

Habilite o provedor do Cofre de Chaves do Azure em sua assinatura do Azure e crie um grupo de recursos. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `WestUS`:

```azurecli
azure provider register Microsoft.KeyVault
azure group create myResourceGroup --location WestUS
```

O Cofre de chaves do Azure que contém as chaves criptográficas e recursos de computação associados, como armazenamento e a própria VM deve residir na mesma região. O exemplo a seguir cria um Cofre de Chaves do Azure chamado `myKeyVault`:

```azurecli
azure keyvault create --vault-name myKeyVault --resource-group myResourceGroup \
  --location WestUS
```

É possível armazenar chaves de criptografia usando a proteção do Modelo de segurança de Hardware (HSM) ou software. Usar um HSM requer um Cofre de Chaves premium. Há um custo adicional para a criação de um Cofre de Chaves premium em vez do Cofre de Chaves padrão que armazena as chaves protegidas por software. Para criar um Cofre de Chaves premium, na etapa anterior, adicione `--sku Premium` ao comando. O exemplo a seguir usa chaves protegidas por software, desde que criamos um Cofre de Chaves padrão.

Para ambos os modelos de proteção, a plataforma do Azure deve ter acesso para solicitar as chaves criptográficas quando a VM é inicializada para descriptografar os discos virtuais. Crie uma chave de criptografia dentro de seu Cofre de Chaves e habilite-a para uso com criptografia de disco virtual. O exemplo a seguir cria uma chave chamada `myKey` e, em seguida, a habilita para a criptografia de disco:

```azurecli
azure keyvault key create --vault-name myKeyVault --key-name myKey \
  --destination software
azure keyvault set-policy --vault-name myKeyVault --resource-group myResourceGroup \
  --enabled-for-disk-encryption true
```


## <a name="create-the-azure-active-directory-application"></a>Criar o aplicativo Azure Active Directory
Quando os discos virtuais são criptografados ou descriptografados, use um ponto de extremidade para lidar com a autenticação e a troca de chaves criptográficas do Cofre de Chaves. Esse ponto de extremidade, um aplicativo do Azure Active Directory, permite que a plataforma do Azure solicite as chaves criptográficas apropriadas em nome da VM. Uma instância do Azure Active Directory padrão está disponível em sua assinatura, embora muitas organizações tenham diretórios do Azure Active Directory dedicados.

Como você não está criando um aplicativo completo do Azure Active Directory, os parâmetros `--home-page` e `--identifier-uris` no exemplo a seguir não precisam ser um endereço roteável real. O exemplo a seguir também especifica um segredo baseado em senha, em vez de gerar chaves de dentro do portal do Azure. Neste momento, a geração de chaves não pode ser feita na CLI do Azure.

Crie seu próprio aplicativo do Azure Active Directory. O exemplo a seguir cria um aplicativo chamado `myAADApp` e usa uma senha de `myPassword`. Especifique sua própria senha da seguinte maneira:

```azurecli
azure ad app create --name myAADApp \
  --home-page http://testencrypt.contoso.com \
  --identifier-uris http://testencrypt.contoso.com \
  --password myPassword
```

Anote o `applicationId` que é retornado na saída do comando anterior. A ID desse aplicativo é usada em algumas das etapas restantes. Em seguida, crie um nome da entidade de serviço (SPN) para que o aplicativo possa ser acessado no seu ambiente. Para criptografar ou descriptografar discos virtuais com êxito, as permissões na chave de criptografia armazenada no Cofre de chaves devem ser definidas para permitir que o aplicativo do Azure Active Directory leia as chaves.

Crie o SPN e defina as permissões adequadas da seguinte maneira:

```azurecli
azure ad sp create --applicationId myApplicationID
azure keyvault set-policy --vault-name myKeyVault --spn myApplicationID \
  --perms-to-keys [\"all\"] --perms-to-secrets [\"all\"]
```


## <a name="add-a-virtual-disk-and-review-encryption-status"></a>Adicione um disco virtual e examine o status da criptografia
Para criptografar alguns discos virtuais, vamos adicionar um disco a uma VM existente. Adicione um disco de 5Gb de dados a uma VM existente da seguinte maneira:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Os discos virtuais não estão criptografados no momento. Examine o status atual de criptografia da sua VM da seguinte maneira:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="encrypt-virtual-disks"></a>Criptografar discos virtuais
Para criptografar agora os discos virtuais, reúna todos os componentes anteriores:

1. Especifique o aplicativo do Azure Active Directory e senha.
2. Especifique o Cofre de chaves para armazenar os metadados para os discos criptografados.
3. Especifique as chaves de criptografia a serem usadas para criptografia e descriptografia.
4. Especifique se deseja criptografar o disco do sistema operacional, os discos de dados ou todos.

Vamos examinar os detalhes de seu Cofre de Chaves do Azure e a chave criada, pois você precisará da ID do Cofre de Chaves, do URI e do URL da chave na etapa final:

```azurecli
azure keyvault show myKeyVault
azure keyvault key show myKeyVault myKey
```

Criptografe seus discos virtuais usando a saída dos comandos `azure keyvault show` e `azure keyvault key show` da seguinte maneira:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
```

Como o comando anterior tem muitas variáveis, o exemplo a seguir é o comando completo para referência:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id 147bc426-595d-4bad-b267-58a7cbd8e0b6 \
  --aad-client-secret P@ssw0rd! \
  --disk-encryption-key-vault-url https://myKeyVault.vault.azure.net/ \
  --disk-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --key-encryption-key-url https://myKeyVault.vault.azure.net/keys/myKey/6f5fe9383f4e42d0a41553ebc6a82dd1 \
  --key-encryption-key-vault-id /subscriptions/guid/resourceGroups/myResoureGroup/providers/Microsoft.KeyVault/vaults/myKeyVault \
  --volume-type Data
```

A CLI do Azure não fornece erros detalhados durante o processo de criptografia. Para obter informações adicionais de solução de problemas, examine `/var/log/azure/Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux/0.x.x.x/extension.log` na VM que está criptografando.

Finalmente, vamos examinar o status de criptografia novamente para confirmar se os discos virtuais agora estão criptografados:

```azurecli
azure vm show-disk-encryption-status --resource-group myResourceGroup --name myVM
```


## <a name="add-additional-data-disks"></a>Adicionar discos de dados adicionais
Depois de criptografar seus discos de dados, é possível adicionar discos virtuais à sua VM e criptografá-los também. Quando você executa o comando `azure vm enable-disk-encryption`, incremente a versão de sequência usando o parâmetro `--sequence-version`. Esse parâmetro de versão de sequência permite que você execute operações repetidas na mesma VM.

Por exemplo, permite adicionar um segundo disco virtual à VM da seguinte maneira:

```azurecli
azure vm disk attach-new --resource-group myResourceGroup --vm-name myVM \
  --size-in-gb 5
```

Execute novamente o comando para criptografar os discos virtuais, dessa vez, adicionando o parâmetro `--sequence-version` e incrementando o valor de nossa primeira execução da seguinte maneira:

```azurecli
azure vm enable-disk-encryption --resource-group myResourceGroup --name myVM \
  --aad-client-id myApplicationID --aad-client-secret myApplicationPassword \
  --disk-encryption-key-vault-url myKeyVaultVaultURI \
  --disk-encryption-key-vault-id myKeyVaultID \
  --key-encryption-key-url myKeyKID \
  --key-encryption-key-vault-id myKeyVaultID \
  --volume-type Data
  --sequence-version 2
```


## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre como gerenciar o Cofre de Chaves do Azure, incluindo a exclusão de chaves criptográficas e cofres, consulte [Gerenciar Cofres de Chaves usando a CLI](../../key-vault/key-vault-manage-with-cli2.md).
* Para obter mais informações sobre criptografia de disco, como preparar uma VM personalizada criptografada para carregar no Azure, consulte [Azure Disk Encryption](../../security/azure-security-disk-encryption.md).
