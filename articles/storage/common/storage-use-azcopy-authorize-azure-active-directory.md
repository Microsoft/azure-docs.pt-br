---
title: Autorizar o acesso a BLOBs com o AzCopy & Azure Active Directory | Microsoft Docs
description: Você pode fornecer credenciais de autorização para operações de AzCopy usando o Azure Active Directory (Azure AD).
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/17/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 99e06a36c2afa66f2874c14990d50c6287623efd
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672484"
---
# <a name="authorize-access-to-blobs-with-azcopy-and-azure-active-directory-azure-ad"></a>Autorizar o acesso a BLOBs com AzCopy e Azure Active Directory (Azure AD)

Você pode fornecer AzCopy com credenciais de autorização usando o Azure AD. Dessa forma, você não precisará acrescentar um token SAS (assinatura de acesso compartilhado) a cada comando. 

Comece verificando suas atribuições de função. Em seguida, escolha o tipo de _entidade de segurança_ que você deseja autorizar. Uma [identidade de usuário](../../active-directory/fundamentals/add-users-azure-active-directory.md), uma [identidade gerenciada](../../active-directory/managed-identities-azure-resources/overview.md)e uma [entidade de serviço](../../active-directory/develop/app-objects-and-service-principals.md) são cada um tipo de entidade de segurança.

Uma identidade de usuário é qualquer usuário que tenha uma identidade no Azure AD. É a entidade de segurança mais fácil de autorizar. Identidades gerenciadas e entidades de serviço são ótimas opções se você planeja usar o AzCopy dentro de um script que é executado sem interação com o usuário. Uma identidade gerenciada é mais adequada para scripts que são executados de uma VM (máquina virtual) do Azure, e uma entidade de serviço é mais adequada para scripts que são executados localmente. 

Para obter mais informações sobre AzCopy, comece a usar o [AzCopy](storage-use-azcopy-v10.md).

## <a name="verify-role-assignments"></a>Verificar atribuições de função

O nível de autorização de que você precisa se baseia se você planeja carregar arquivos ou apenas baixá-los.

Se você quiser apenas baixar arquivos, verifique se a função de [leitor de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) foi atribuída à sua identidade de usuário, identidade gerenciada ou entidade de serviço.

Se você quiser carregar arquivos, verifique se uma dessas funções foi atribuída à sua entidade de segurança:

- [Colaborador de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- [Proprietário de Dados do Blob de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

Essas funções podem ser atribuídas à entidade de segurança em qualquer um desses escopos:

- Contêiner (sistema de arquivos)
- Conta de armazenamento
- Resource group
- Subscription

Para saber como verificar e atribuir funções, consulte [usar o portal do Azure para atribuir uma função do Azure para acessar dados de BLOB e de fila](./storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Tenha em mente que as atribuições de função do Azure podem levar até cinco minutos para serem propagadas.

Você não precisará ter uma dessas funções atribuídas à entidade de segurança se sua entidade de segurança for adicionada à lista de controle de acesso (ACL) do contêiner ou diretório de destino. Na ACL, sua entidade de segurança precisa de permissão de gravação no diretório de destino e permissão de execução no contêiner e em cada diretório pai.

Para saber mais, consulte [modelo de controle de acesso em Azure data Lake Storage Gen2](../blobs/data-lake-storage-access-control-model.md).

## <a name="authorize-a-user-identity"></a>Autorizar uma identidade de usuário

Depois de verificar se a identidade do usuário recebeu o nível de autorização necessário, abra um prompt de comando, digite o comando a seguir e pressione a tecla ENTER.

```azcopy
azcopy login
```

Se você receber um erro, tente incluir a ID de locatário da organização à qual a conta de armazenamento pertence.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Substitua o `<tenant-id>` espaço reservado pela ID de locatário da organização à qual a conta de armazenamento pertence. Para localizar a ID de locatário, selecione **Azure Active Directory Propriedades de > > ID de diretório** no portal do Azure.

Esse comando retorna um código de autenticação e a URL de um site. Abra o site, forneça o código e, em seguida, escolha o botão **Avançar**.

![Criar um contêiner](media/storage-use-azcopy-v10/azcopy-login.png)

Uma janela de entrada será exibida. Nessa janela, entre em sua conta do Azure usando suas credenciais de conta do Azure. Depois de entrar com êxito, feche a janela do navegador e comece a usar o AzCopy.

<a id="managed-identity"></a>

## <a name="authorize-a-managed-identity"></a>Autorizar uma identidade gerenciada

Essa é uma ótima opção se você planeja usar o AzCopy dentro de um script que é executado sem interação do usuário e o script é executado de uma VM (máquina virtual) do Azure. Ao usar essa opção, você não precisará armazenar nenhuma credencial na VM.

Você pode entrar em sua conta usando uma identidade gerenciada de todo o sistema que você habilitou em sua VM ou usando a ID do cliente, a ID de objeto ou a ID de recurso de uma identidade gerenciada atribuída pelo usuário que você atribuiu à sua VM.

Para saber mais sobre como habilitar uma identidade gerenciada em todo o sistema ou criar uma identidade gerenciada atribuída pelo usuário, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

#### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Autorizar usando uma identidade gerenciada em todo o sistema

Primeiro, verifique se você habilitou uma identidade gerenciada em todo o sistema em sua VM. Consulte [identidade gerenciada atribuída pelo sistema](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Em seguida, no console de comando, digite o comando a seguir e pressione a tecla ENTER.

```azcopy
azcopy login --identity
```

#### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Autorizar usando uma identidade gerenciada atribuída pelo usuário

Primeiro, verifique se você habilitou uma identidade gerenciada atribuída pelo usuário em sua VM. Consulte [identidade gerenciada atribuída pelo usuário](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Em seguida, no console de comando, digite qualquer um dos comandos a seguir e pressione a tecla ENTER.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Substitua o `<client-id>` espaço reservado pela ID do cliente da identidade gerenciada atribuída pelo usuário.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Substitua o `<object-id>` espaço reservado pela ID de objeto da identidade gerenciada atribuída pelo usuário.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Substitua o `<resource-id>` espaço reservado pela ID de recurso da identidade gerenciada atribuída pelo usuário.

<a id="service-principal"></a>

## <a name="authorize-a-service-principal"></a>Autorizar uma entidade de serviço

Essa é uma ótima opção se você planeja usar o AzCopy dentro de um script que é executado sem interação do usuário, especialmente quando executado no local. Se você planeja executar o AzCopy em VMs que são executadas no Azure, uma identidade de serviço gerenciada é mais fácil de administrar. Para saber mais, consulte a seção [autorizar uma identidade gerenciada](#authorize-a-managed-identity) deste artigo.

Antes de executar um script, você precisa entrar interativamente pelo menos uma vez para que possa fornecer AzCopy com as credenciais de sua entidade de serviço.  Essas credenciais são armazenadas em um arquivo seguro e criptografado para que o script não precise fornecer essas informações confidenciais.

Você pode entrar em sua conta usando um segredo do cliente ou usando a senha de um certificado associado ao registro do aplicativo da sua entidade de serviço.

Para saber mais sobre como criar a entidade de serviço, consulte [como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que possa acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md).

Para saber mais sobre as entidades de serviço em geral, consulte [objetos de aplicativo e entidade de serviço no Azure Active Directory](../../active-directory/develop/app-objects-and-service-principals.md)

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>Autorizar uma entidade de serviço usando um segredo do cliente

Comece definindo a `AZCOPY_SPA_CLIENT_SECRET` variável de ambiente como o segredo do cliente do registro do aplicativo da sua entidade de serviço.

> [!NOTE]
> Certifique-se de definir esse valor no prompt de comando e não nas configurações de variável de ambiente do seu sistema operacional. Dessa forma, o valor estará disponível somente para a sessão atual.

Este exemplo mostra como você pode fazer isso no PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Considere usar um prompt, conforme mostrado neste exemplo. Dessa forma, sua senha não aparecerá no histórico de comandos do console.  

Em seguida, digite o comando a seguir e pressione a tecla ENTER.

```azcopy
azcopy login --service-principal  --application-id application-id --tenant-id=tenant-id
```

Substitua o `<application-id>` espaço reservado pela ID do aplicativo do registro do aplicativo da sua entidade de serviço. Substitua o `<tenant-id>` espaço reservado pela ID de locatário da organização à qual a conta de armazenamento pertence. Para localizar a ID de locatário, selecione **Azure Active Directory Propriedades de > > ID de diretório** no portal do Azure. 

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Autorizar uma entidade de serviço usando um certificado

Se preferir usar suas próprias credenciais para autorização, você poderá carregar um certificado para o registro do aplicativo e, em seguida, usar esse certificado para fazer logon.

Além de carregar seu certificado para o registro do aplicativo, você também precisará ter uma cópia do certificado salvo no computador ou na VM em que o AzCopy será executado. Esta cópia do certificado deve estar no. PFX ou. O formato PEM e deve incluir a chave privada. A chave privada deve ser protegida por senha. Se você estiver usando o Windows e seu certificado existir somente em um repositório de certificados, certifique-se de exportar esse certificado para um arquivo PFX (incluindo a chave privada). Para obter diretrizes, consulte [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate)

Em seguida, defina a `AZCOPY_SPA_CERT_PASSWORD` variável de ambiente como a senha do certificado.

> [!NOTE]
> Certifique-se de definir esse valor no prompt de comando e não nas configurações de variável de ambiente do seu sistema operacional. Dessa forma, o valor estará disponível somente para a sessão atual.

Este exemplo mostra como você pode executar essa tarefa no PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Em seguida, digite o comando a seguir e pressione a tecla ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Substitua o `<path-to-certificate-file>` espaço reservado pelo caminho relativo ou totalmente qualificado para o arquivo de certificado. AzCopy salva o caminho para esse certificado, mas não salva uma cópia do certificado, portanto, certifique-se de manter esse certificado em vigor. Substitua o `<tenant-id>` espaço reservado pela ID de locatário da organização à qual a conta de armazenamento pertence. Para localizar a ID de locatário, selecione **Azure Active Directory Propriedades de > > ID de diretório** no portal do Azure.

> [!NOTE]
> Considere usar um prompt, conforme mostrado neste exemplo. Dessa forma, sua senha não aparecerá no histórico de comandos do console. 

## <a name="authorize-without-a-secret-store"></a>Autorizar sem um repositório secreto

O `azcopy login` comando recupera um token OAuth e, em seguida, coloca esse token em um repositório secreto em seu sistema. Se o seu sistema operacional não tiver um repositório secreto, como um *keyring* do Linux, o `azcopy login` comando não funcionará porque não há lugar para posicionar o token. 

Em vez de usar o `azcopy login` comando, você pode definir variáveis de ambiente na memória. Em seguida, execute qualquer comando AzCopy. AzCopy recuperará o token de autenticação necessário para concluir a operação. Após a conclusão da operação, o token desaparece da memória. 

### <a name="authorize-a-user-identity"></a>Autorizar uma identidade de usuário

Depois de verificar se a identidade do usuário recebeu o nível de autorização necessário, digite o comando a seguir e pressione a tecla ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=DEVICE
```

Em seguida, execute qualquer comando azcopy (por exemplo: `azcopy list https://contoso.blob.core.windows.net` ).

Esse comando retorna um código de autenticação e a URL de um site. Abra o site, forneça o código e, em seguida, escolha o botão **Avançar**.

![Criar um contêiner](media/storage-use-azcopy-v10/azcopy-login.png)

Uma janela de entrada será exibida. Nessa janela, entre em sua conta do Azure usando suas credenciais de conta do Azure. Depois de entrar com êxito, a operação pode ser concluída.

### <a name="authorize-by-using-a-system-wide-managed-identity"></a>Autorizar usando uma identidade gerenciada em todo o sistema

Primeiro, verifique se você habilitou uma identidade gerenciada em todo o sistema em sua VM. Consulte [identidade gerenciada atribuída pelo sistema](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity).

Digite o comando a seguir e pressione a tecla ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

Em seguida, execute qualquer comando azcopy (por exemplo: `azcopy list https://contoso.blob.core.windows.net` ).

### <a name="authorize-by-using-a-user-assigned-managed-identity"></a>Autorizar usando uma identidade gerenciada atribuída pelo usuário

Primeiro, verifique se você habilitou uma identidade gerenciada atribuída pelo usuário em sua VM. Consulte [identidade gerenciada atribuída pelo usuário](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#user-assigned-managed-identity).

Digite o comando a seguir e pressione a tecla ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=MSI
```

Em seguida, digite qualquer um dos comandos a seguir e pressione a tecla ENTER.

```bash
export AZCOPY_MSI_CLIENT_ID=<client-id>
```

Substitua o `<client-id>` espaço reservado pela ID do cliente da identidade gerenciada atribuída pelo usuário.

```bash
export AZCOPY_MSI_OBJECT_ID=<object-id>
```

Substitua o `<object-id>` espaço reservado pela ID de objeto da identidade gerenciada atribuída pelo usuário.

```bash
export AZCOPY_MSI_RESOURCE_STRING=<resource-id>
```

Substitua o `<resource-id>` espaço reservado pela ID de recurso da identidade gerenciada atribuída pelo usuário.

Depois de definir essas variáveis, você pode executar qualquer comando azcopy (por exemplo: `azcopy list https://contoso.blob.core.windows.net` ).

### <a name="authorize-a-service-principal"></a>Autorizar uma entidade de serviço

Você pode entrar em sua conta usando um segredo do cliente ou usando a senha de um certificado associado ao registro do aplicativo da sua entidade de serviço.

#### <a name="authorize-a-service-principal-by-using-a-client-secret"></a>Autorizar uma entidade de serviço usando um segredo do cliente

Digite o comando a seguir e pressione a tecla ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_APPLICATION_ID=<application-id>
export AZCOPY_SPA_CLIENT_SECRET=<client-secret>
```

Substitua o `<application-id>` espaço reservado pela ID do aplicativo do registro do aplicativo da sua entidade de serviço. Substitua o `<client-secret>` espaço reservado pelo segredo do cliente.

> [!NOTE]
> Considere o uso de uma solicitação para coletar a senha do usuário. Dessa forma, sua senha não aparecerá no seu histórico de comandos. 

Em seguida, execute qualquer comando azcopy (por exemplo: `azcopy list https://contoso.blob.core.windows.net` ).

#### <a name="authorize-a-service-principal-by-using-a-certificate"></a>Autorizar uma entidade de serviço usando um certificado

Se preferir usar suas próprias credenciais para autorização, você poderá carregar um certificado para o registro do aplicativo e, em seguida, usar esse certificado para fazer logon.

Além de carregar seu certificado para o registro do aplicativo, você também precisará ter uma cópia do certificado salvo no computador ou na VM em que o AzCopy será executado. Esta cópia do certificado deve estar no. PFX ou. O formato PEM e deve incluir a chave privada. A chave privada deve ser protegida por senha. 

Digite o comando a seguir e pressione a tecla ENTER.

```bash
export AZCOPY_AUTO_LOGIN_TYPE=SPN
export AZCOPY_SPA_CERT_PATH=<path-to-certificate-file>
export AZCOPY_SPA_CERT_PASSWORD=<certificate-password>
```

Substitua o `<path-to-certificate-file>` espaço reservado pelo caminho relativo ou totalmente qualificado para o arquivo de certificado. AzCopy salva o caminho para esse certificado, mas não salva uma cópia do certificado, portanto, certifique-se de manter esse certificado em vigor. Substitua o `<certificate-password>` espaço reservado pela senha do certificado.

> [!NOTE]
> Considere o uso de uma solicitação para coletar a senha do usuário. Dessa forma, sua senha não aparecerá no seu histórico de comandos. 

Em seguida, execute qualquer comando azcopy (por exemplo: `azcopy list https://contoso.blob.core.windows.net` ).

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre AzCopy, [comece a](storage-use-azcopy-v10.md) usar o AzCopy

- Se você tiver dúvidas, problemas ou comentários gerais, envie-os [na página do GitHub](https://github.com/Azure/azure-storage-azcopy) .