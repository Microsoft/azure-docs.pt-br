---
title: "Configurar um nome de domínio personalizado para seu ponto de extremidade de Armazenamento de Blobs do Azure| Microsoft Docs"
description: "Use o Portal do Azure para mapear seu próprio nome canônico (CNAME) para o ponto de extremidade do Armazenamento de Blobs em uma conta de Armazenamento do Azure."
services: storage
documentationcenter: 
author: tamram
manager: timlt
editor: tysonn
ms.assetid: aaafd8c5-eacb-49dc-8c8b-3f7011ad5e92
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2017
ms.author: tamram
ms.openlocfilehash: cbc8654bf1755826afa2cf83e5476e88903e0854
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-custom-domain-name-for-your-blob-storage-endpoint"></a>Configurar um nome de domínio personalizado para seu ponto de extremidade de Armazenamento de Blobs

Você pode configurar um domínio personalizado para acessar os dados de blob em sua conta de armazenamento do Azure. O ponto de extremidade para o Armazenamento de Blobs é `<storage-account-name>.blob.core.windows.net`. Se você mapear um domínio personalizado e um subdomínio como **www.contoso.com** para o ponto de extremidade do blob para sua conta de armazenamento, os usuários também poderão acessar dados do blob em sua conta de armazenamento usando esse domínio.

> [!IMPORTANT]
> O Armazenamento do Azure ainda não dá suporte nativo a HTTPS com domínios personalizados. No momento, você pode [Usar a CDN do Azure para acessar blobs com domínios personalizados por HTTPS](storage-https-custom-domain-cdn.md).
>

A tabela a seguir mostra exemplos de URLs para acessar os dados do blob em uma conta de armazenamento denominada **mystorageaccount**. O domínio personalizado registrado para a conta de armazenamento é **www.contoso.com**.

| Tipo de recurso | URL padrão | URL de domínio personalizada |
| --- | --- | --- |
| Conta de armazenamento | http://mystorageaccount.blob.core.windows.net | http://www.contoso.com |
| Blob |http://mystorageaccount.blob.core.windows.net/mycontainer/myblob | http://www.contoso.com/mycontainer/myblob |
| Contêiner raiz | http://mystorageaccount.blob.core.windows.net/myblob ou http://mystorageaccount.blob.core.windows.net/$root/myblob| http://www.contoso.com/myblob ou http://www.contoso.com/$root/myblob |

## <a name="direct-vs-intermediary-domain-mapping"></a>Mapeamento de domínio direto versus intermediário

Há duas maneiras de apontar seu domínio personalizado para o ponto de extremidade de blob para sua conta de armazenamento: mapeamento de CNAME direto e usar o subdomínio intermediário *asverify*.

### <a name="direct-cname-mapping"></a>Mapeamento direto de CNAME

O primeiro método, que é também o mais simples, é criar um registro de nome canônico (CNAME) que mapeia seu domínio e subdomínio personalizados diretamente para o ponto de extremidade do blob. Um registro CNAME é um recurso de DNS que mapeia um domínio de origem a um domínio de destino. Nesse caso, o domínio de origem é seu próprio domínio e subdomínio personalizados, por exemplo *www.contoso.com*. O domínio de destino é o ponto de extremidade de serviço Blob, por exemplo *mystorageaccount.blob.core.windows.net*.

O método direto é abordado em [Registrar um domínio personalizado](#register-a-custom-domain).

### <a name="intermediary-mapping-with-asverify"></a>Mapeamento intermediário com *asverify*

O segundo método também usa registros CNAME, mas primeiro emprega um subdomínio especial reconhecido pelo Azure para evitar tempo de inatividade: **asverify**.

O processo de mapear seu domínio personalizado para um ponto de extremidade de blobs pode, no entanto, resultar em um breve período de inatividade para o domínio, enquanto você está registrando o domínio no [Portal do Azure](https://portal.azure.com). Se atualmente seu domínio personalizado dá suporte a um aplicativo com um SLA (contrato de nível de serviço) que exige tempo de inatividade zero, você pode usar o subdomínio *asverify* do Azure como uma etapa intermediária de registro. Essa etapa intermediária garante que os usuários possam acessar seu domínio enquanto ocorre o mapeamento do DNS.

O método intermediário é abordado em [Registrar um domínio personalizado usando o subdomínio *asverify*](#register-a-custom-domain-using-the-asverify-subdomain).

## <a name="register-a-custom-domain"></a>Registrar um domínio personalizado
Use este procedimento para registrar seu domínio personalizado se você não se importar com a indisponibilidade temporária do domínio para os usuários ou se seu domínio personalizado não estiver hospedando um aplicativo no momento.

Se seu domínio personalizado der suporte atualmente a um aplicativo que não pode ter nenhum tempo de inatividade, use o procedimento descrito em [Registrar um domínio personalizado usando o subdomínio *asverify*](#register-a-custom-domain-using-the-asverify-subdomain).

Para configurar um nome de domínio personalizado, você deve criar um novo registro CNAME no DNS. O registro CNAME especifica um alias para um nome de domínio. Nesse caso, ele mapeia o endereço do seu domínio personalizado ao ponto de extremidade de Armazenamento de Blobs para sua conta de armazenamento.

Normalmente, você pode gerenciar as configurações de DNS do seu domínio no site do registrador de domínios. Cada registrador tem um método semelhante, mas ligeiramente diferente, para especificar um registro CNAME, mas o conceito é o mesmo. Alguns pacotes de registro de domínio básicos não oferecem a configuração do DNS, portanto, pode ser necessário atualizar o pacote de registro de domínio antes de criar o registro CNAME.

1. Navegue até sua conta de armazenamento no [portal do Azure](https://portal.azure.com).
1. Em **SERVIÇO BLOB** na folha do menu, selecione **Domínio personalizado** para abrir a folha *Domínio personalizado*.
1. Faça logon no site do registrador de domínios e acesse a página de gerenciamento de DNS. Você pode encontrá-lo em uma seção como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**.
1. Localize a seção de gerenciamento de CNAMEs. Talvez você precise acessar uma página de configurações avançadas e procurar as palavras **CNAME**, **Alias** ou **Subdomínios**.
1. Crie um novo registro CNAME e forneça um alias de subdomínio, como **www** ou **photos**. Em seguida, forneça um nome do host, que é o ponto de extremidade de serviço Blob, no formato **mystorageaccount.blob.core.windows.net** (em que *mystorageaccount* é o nome da sua conta de armazenamento). O nome de host a ser usado aparece no item nº 1 da folha *Domínio personalizado* no [Portal do Azure](https://portal.azure.com).
1. Na caixa de texto na folha *Domínio personalizado* no [Portal do Azure](https://portal.azure.com), digite o nome do seu domínio personalizado, incluindo o subdomínio. Por exemplo, se o domínio for **contoso.com** e o alias de subdomínio for **www**, digite **www.contoso.com**. Se o seu subdomínio for **photos**, digite **photos.contoso.com**. O subdomínio é *obrigatório*.
1. Selecione **Salvar** na folha *Domínio personalizado* para registrar seu domínio personalizado. Se o registro for bem-sucedido, você verá uma notificação no portal de que sua conta de armazenamento foi atualizada com êxito.

Depois que o novo registro CNAME for propagado pelo DNS, os usuários poderão exibir dados de blobs usando seu domínio personalizado, desde que tenham as permissões apropriadas.

## <a name="register-a-custom-domain-using-the-asverify-subdomain"></a>Registrar um domínio personalizado usando o subdomínio *asverify*
Use este procedimento para registrar seu personalizado domínio se o seu domínio personalizado atualmente der suporte a um aplicativo com um SLA que não permita nenhum tempo de inatividade. Criando um CNAME que aponta de `asverify.<subdomain>.<customdomain>` para `asverify.<storageaccount>.blob.core.windows.net`, você pode registrar previamente seu domínio com o Azure. Depois, é possível criar um segundo CNAME que aponta de `<subdomain>.<customdomain>` para `<storageaccount>.blob.core.windows.net`, quando então o tráfego de ponto para seu domínio personalizado será direcionado para o ponto de extremidade do blob.

O subdomínio **asverify** é um subdomínio especial reconhecido pelo Azure. Ao preceder `asverify` em seu próprio subdomínio, você permite que o Azure reconheça seu domínio personalizado sem modificar o registro DNS dele. Quando você modificar o registro DNS do domínio, ele será mapeado para o ponto de extremidade do blob sem nenhum tempo de inatividade.

1. Navegue até sua conta de armazenamento no [portal do Azure](https://portal.azure.com).
1. Em **SERVIÇO BLOB** na folha do menu, selecione **Domínio personalizado** para abrir a folha *Domínio personalizado*.
1. Faça logon no site do provedor de DNS e acesse a página de gerenciamento de DNS. Você pode encontrá-lo em uma seção como **Nome de Domínio**, **DNS** ou **Gerenciamento de Servidor de Nomes**.
1. Localize a seção de gerenciamento de CNAMEs. Talvez você precise acessar uma página de configurações avançadas e procurar as palavras **CNAME**, **Alias** ou **Subdomínios**.
1. Crie um novo registro CNAME e forneça um alias de subdomínio que inclui o subdomínio *asverify*. Por exemplo, **asverify. www** ou **asverify.photos**. Em seguida, forneça um nome do host, que é o ponto de extremidade de serviço Blob, no formato **asverify.mystorageaccount.blob.core.windows.net** (em que **mystorageaccount** é o nome da sua conta de armazenamento). O nome de host a ser usado aparece no item nº 2 da folha *Domínio personalizado* no [Portal do Azure](https://portal.azure.com).
1. Na caixa de texto na folha *Domínio personalizado* no [Portal do Azure](https://portal.azure.com), digite o nome do seu domínio personalizado, incluindo o subdomínio. Não inclua *asverify*. Por exemplo, se o domínio for **contoso.com** e o alias de subdomínio for **www**, digite **www.contoso.com**. Se o seu subdomínio for **photos**, digite **photos.contoso.com**. O subdomínio é obrigatório.
1. Marque a caixa de seleção **Usar validação indireta de CNAME**.
1. Selecione **Salvar** na folha *Domínio personalizado* para registrar seu domínio personalizado. Se o registro for bem-sucedido, você verá uma notificação no portal informando que sua conta de armazenamento foi atualizada com êxito. Nesse ponto, seu domínio personalizado foi verificado pelo Azure, mas o tráfego para seu domínio ainda não está sendo roteado para sua conta de armazenamento.
1. Volte ao site do provedor de DNS e crie outro registro CNAME que mapeia seu subdomínio para o ponto de extremidade do serviço Blob. Por exemplo, especifique o subdomínio como **www** ou **photos** (sem o *asverify*) e o nome do host como **mystorageaccount.blob.core.windows.net** (em que **mystorageaccount** é o nome da sua conta de armazenamento). Com essa etapa, o registro do seu domínio personalizado está concluído.
1. Por fim, você pode excluir o registro CNAME criado contendo o subdomínio **asverify**, já que ele era necessário apenas como uma etapa intermediária.

Depois que o novo registro CNAME for propagado pelo DNS, os usuários poderão exibir dados de blobs usando seu domínio personalizado, desde que tenham as permissões apropriadas.

## <a name="test-your-custom-domain"></a>Testar seu domínio personalizado

Para confirmar se seu domínio personalizado está de fato mapeado para o ponto de extremidade do serviço Blob, crie um blob em um contêiner público em sua conta de armazenamento. Em seguida, em um navegador da Web, use um URI no seguinte formato para acessar o blob:

`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Por exemplo, você pode usar o seguinte URI para acessar um formulário da Web por meio de um contêiner **myforms** no subdomínio personalizado **photos.contoso.com**:

`http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Cancelar o registro de um domínio personalizado

Para cancelar o registro de um domínio personalizado para seu ponto de extremidade do Armazenamento de Blobs, use um dos procedimentos a seguir.

### <a name="azure-portal"></a>Portal do Azure

Execute o seguinte no Portal do Azure para remover a configuração de domínio personalizado:

1. Navegue até sua conta de armazenamento no [portal do Azure](https://portal.azure.com).
1. Em **SERVIÇO BLOB** na folha do menu, selecione **Domínio personalizado** para abrir a folha *Domínio personalizado*.
1. Limpe o conteúdo da caixa de texto que contém o nome de domínio personalizado.
1. Selecione o botão **Salvar**.

Após a remoção do domínio personalizado, você verá uma notificação no portal informando que sua conta de armazenamento foi atualizada com êxito.

### <a name="azure-cli-20"></a>CLI 2.0 do Azure

Use o comando de CLI [az storage account update](https://docs.microsoft.com/cli/azure/storage/account#az_storage_account_update) e especifique uma cadeia de caracteres vazia (`""`) para o valor do argumento `--custom-domain` para remover um registro de domínio personalizado.

* Formato do comando:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Exemplo de comando:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

### <a name="powershell"></a>PowerShell

Use o cmdlet do PowerShell [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount) e especifique uma cadeia de caracteres vazia (`""`) para o valor do argumento `-CustomDomainName` para remover um registro de domínio personalizado.

* Formato do comando:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Exemplo de comando:

  ```powershell
  Set-AzureRmStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Próximas etapas
* [Mapear o domínio personalizado para o ponto de extremidade de uma CDN (Rede de Distribuição de Conteúdo) do Azure](../../cdn/cdn-map-content-to-custom-domain.md)
* [Usar a CDN do Azure para acessar blobs com domínios personalizados por HTTPS](storage-https-custom-domain-cdn.md)
