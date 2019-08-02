---
title: Configurar um nome de domínio personalizado para sua conta de armazenamento do Azure | Microsoft Docs
description: Use o portal do Azure para mapear seu próprio nome canônico (CNAME) para o armazenamento de Blob ou o ponto de extremidade da Web em uma conta de armazenamento do Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 06/26/2018
ms.author: normesta
ms.reviewer: seguler
ms.subservice: blobs
ms.openlocfilehash: c0fb3551b14a2239f26c54f639b90400277fab25
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501940"
---
# <a name="configure-a-custom-domain-name-for-your-azure-storage-account"></a>Configurar um nome de domínio personalizado para sua conta de armazenamento do Azure

Você pode configurar um domínio personalizado para acessar os dados de blob em sua conta de armazenamento do Azure. O endpoint padrão do armazenamento de Blobs do Azure é *\<nome da conta de armazenamento>.blob.core.windows.net*. Você também pode usar o ponto de extremidade da web que é gerado como parte de [recurso sites estáticos](storage-blob-static-website.md). Se você mapear um domínio e subdomínio personalizados, como *www\.contoso.com*, para o ponto de extremidade da web ou de blob para sua conta de armazenamento, os usuários podem usar esse domínio para acessar dados de blob em sua conta de armazenamento.

> [!IMPORTANT]
> O Armazenamento do Azure ainda não dá suporte nativo a HTTPS com domínios personalizados. Você pode atualmente [Usar o CDN do Azure para acessar blobs usando domínios personalizados em HTTPS](storage-https-custom-domain-cdn.md).
> 
> 
> [!NOTE]
> Contas de armazenamento atualmente suportam a apenas um nome de domínio personalizado por conta. Você não pode mapear um nome de domínio personalizado para os pontos de extremidade de serviços da Web e de blob.
> 
> [!NOTE]
> O mapeamento só funcionam para subdomínios (por exemplo, www\.contoso.com). Se você quiser ter seu ponto de extremidade da web disponível no domínio raiz (por exemplo, contoso.com), você precisa [usar a CDN do Azure com domínios personalizados](storage-https-custom-domain-cdn.md)

A tabela a seguir mostra algumas URLs de amostra para dados de blobs localizados em uma conta de armazenamento denominada *mystorageaccount*. O subdomínio personalizado que está registrado para a conta de armazenamento é *www\.contoso.com*:

| Tipo de recurso | URL padrão | URL de domínio personalizada |
| --- | --- | --- |
| Conta de armazenamento | http:\//mystorageaccount.blob.core.windows.net | http:\//www.contoso.com |
| Blob |http:\//mystorageaccount.blob.core.windows.net/mycontainer/myblob | http:\//www.contoso.com/mycontainer/myblob |
| Contêiner raiz | http:\//mystorageaccount.blob.core.windows.net/myblob ou http:\//mystorageaccount.blob.core.windows.net/$root/myblob | http:\//www.contoso.com/myblob ou http:\//www.contoso.com/$ raiz/myblob |
| Web |  http:\//mystorageaccount. [ Zone].Web.Core.Windows.NET/$Web/[indexdoc] ou http:\//mystorageaccount. [ Zone].Web.Core.Windows.NET/[indexdoc] ou http:\//mystorageaccount. [ Zone].Web.Core.Windows.NET/$Web ou http:\//mystorageaccount. [ Zone].Web.Core.Windows.NET/ | http:\/web de $ /www.contoso.com/ ou http:\//www.contoso.com/ ou http:\//www.contoso.com/ web de $/ [indexdoc] ou http:\//www.contoso.com/ [indexdoc] |

> [!NOTE]  
> Conforme mostrado nas seções a seguir, todos os exemplos para o terminal de serviço de blob também se aplicam ao terminal de serviço da web.

## <a name="direct-vs-intermediary-cname-mapping"></a>Direto versus intermediário mapeamento de CNAME

Você pode apontar seu domínio personalizado, prefixado com um subdomínio (por exemplo, www\.contoso.com) para o ponto de extremidade de blob para sua conta de armazenamento em qualquer uma das duas maneiras: 
* Use o mapeamento de CNAME direto.
* Use o subdomínio intermediário *asverify*.

### <a name="direct-cname-mapping"></a>Mapeamento direto de CNAME

O primeiro método, que é também o mais simples, é criar um registro de nome canônico (CNAME) que mapeia seu domínio e subdomínio personalizados diretamente para o ponto de extremidade do blob. Um registro CNAME é um recurso de DNS que mapeia um domínio de origem a um domínio de destino. Em nosso exemplo, o domínio de origem é seu próprio domínio e subdomínio personalizados (*www\.contoso.com*, por exemplo). O domínio de destino é o ponto de extremidade de serviço de blob (*mystorageaccount.blob.core.windows.net*, por exemplo).

O método direto é coberto na seção "Registrar um domínio personalizado".

### <a name="intermediary-mapping-with-asverify"></a>Mapeamento intermediário com *asverify*

O segundo método também usa registros CNAME. Para evitar o tempo de inatividade, no entanto, ele primeiro emprega um subdomínio especial *asverify* que é reconhecido pelo Azure.

O mapeamento do seu domínio personalizado para um ponto de extremidade de blob pode causar um breve período de inatividade enquanto você registra o domínio no [portal do Microsoft Azure](https://portal.azure.com). Se o domínio atualmente oferecer suporte a um aplicativo com um contrato de nível de serviço (SLA) que exija tempo de inatividade zero, use o subdomínio *asverify* do Azure como uma etapa de registro intermediária. Essa etapa garante que os usuários podem acessar seu domínio enquanto ocorre o mapeamento de DNS.

O método intermediário é coberto em Registre um domínio personalizado usando o subdomínio *asverify*.

## <a name="register-a-custom-domain"></a>Registrar um domínio personalizado
Registre o domínio usando o procedimento nesta seção se as seguintes instruções se aplicarem:
* Você não está preocupado com o fato de o domínio estar brevemente indisponível para seus usuários.
* Seu domínio personalizado não está hospedando um aplicativo no momento. 

Você pode usar o DNS do Azure para configurar um nome DNS personalizado para a sua loja Blob do Azure. Para obter mais informações, consulte [Usar o DNS do Azure para fornecer as configurações de domínio personalizadas para um serviço do Azure](https://docs.microsoft.com/azure/dns/dns-custom-domain#blob-storage).

Se o seu domínio personalizado atualmente suporta um aplicativo que não pode ter nenhum tempo de inatividade, use o procedimento em Registre um domínio personalizado usando o subdomínio *asverify*.

Para configurar um nome de domínio personalizado, crie um novo registro CNAME no DNS. O registro CNAME especifica um alias para um nome de domínio. No nosso exemplo, ele mapeia o endereço do seu domínio personalizado para o endpoint de armazenamento do Blob da sua conta de armazenamento.

Normalmente, você pode gerenciar as configurações de DNS do seu domínio no site do registrador de domínios. Cada registrador tem um método semelhante, mas ligeiramente diferente, para especificar um registro CNAME, mas o conceito é o mesmo. Como alguns pacotes básicos de registro de domínio não oferecem configuração de DNS, talvez seja necessário atualizar seu pacote de registro de domínio para poder criar o registro CNAME.

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.

1. No painel de menu, em **Serviço de Blob**, selecione **Domínio personalizado**.  
   O painel **Domínio personalizado** é aberto.

1. Faça login no site do registrador de domínios e vá para a página de gerenciamento de DNS.  
   Você pode encontrar a página em uma seção denominada **Domain Name**, **DNS** ou **Name Server Management**.

1. Localize a seção de gerenciamento de CNAMEs.  
   Talvez seja necessário acessar uma página de configurações avançadas e procurar **CNAME**, **Alias** ou **Subdomínios**.

1. Criar um novo registro CNAME, insira um alias de subdomínio, como **www** ou **fotos** (subdomínio é obrigatório, não há suporte para domínios raiz) e, em seguida, forneça um nome de host.  
   O nome do host é o ponto de extremidade de serviço de blob. Seu formato é *\<mystorageaccount>.blob.core.windows.net*, onde *mystorageaccount* é o nome da sua conta de armazenamento. O nome do host a ser usado aparece no item #1 do painel **Domínio personalizado** no [portal do Microsoft Azure](https://portal.azure.com). 

1. No painel **Domínio personalizado**, na caixa de texto, insira o nome do seu domínio personalizado, incluindo o subdomínio.  
   Por exemplo, se seu domínio está *contoso.com* e é o alias de subdomínio *www*, insira **www\.contoso.com**. Se o seu subdomínio for *photos*, digite **photos.contoso.com**.

1. Para registrar seu domínio personalizado, selecione **Salvar**.  
   Se o registro for bem-sucedido, o portal notificará que sua conta de armazenamento foi atualizada com sucesso.

Depois que seu novo registro CNAME for propagado pelo DNS, se os usuários tiverem as permissões apropriadas, eles poderão visualizar os dados de blob usando seu domínio personalizado.

## <a name="register-a-custom-domain-by-using-the-asverify-subdomain"></a>Registre um domínio personalizado usando o subdomínio *asverify*
Se o seu domínio personalizado atualmente suporta um aplicativo com um SLA que exige que não haja tempo de inatividade, registre seu domínio personalizado usando o procedimento desta seção. Criando um CNAME que aponta de *comoverify.\<subdomínio>.\<customdomain>* to *asverify.\<storageaccount> .blob.core.windows.net*, você pode pré-registrar seu domínio com o Azure. Você pode criar um segundo CNAME que aponte para o *\<subdomínio>.\<customdomain>* para *\<storageaccount>.blob.core.windows.net* e, em seguida, o tráfego para o seu domínio personalizado é direcionado ao seu ponto de extremidade de blob.

O subdomínio *asverify* é um subdomínio especial reconhecido pelo Azure. Ao adicionar *asverify* ao seu próprio subdomínio, você permite que o Azure reconheça seu domínio personalizado sem precisar modificar o registro DNS do domínio. Quando você modificar o registro DNS do domínio, ele será mapeado para o ponto de extremidade do blob sem nenhum tempo de inatividade.

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.

1. No painel de menu, em **Serviço de Blob**, selecione **Domínio personalizado**.  
   O painel **Domínio personalizado** é aberto.

1. Entre no site do provedor de DNS e vá para a página de gerenciamento de DNS.  
   Você pode encontrar a página em uma seção denominada **Domain Name**, **DNS** ou **Name Server Management**.

1. Localize a seção de gerenciamento de CNAMEs.  
   Talvez seja necessário acessar uma página de configurações avançadas e procurar **CNAME**, **Alias** ou **Subdomínios**.

1. Crie um novo registro CNAME, forneça um alias de subdomínio que inclua o subdomínio *asverify*, como **asverify.www** ou **asverify.photos**, e forneça um nome de host.  
   O nome do host é o ponto de extremidade de serviço de blob. Seu formato é *asverify.\<mystorageaccount> .blob.core.windows.net*, onde *mystorageaccount* é o nome da sua conta de armazenamento. O nome de host a ser usado aparece no item #2 dos *domínio personalizado* painel na [portal do Azure](https://portal.azure.com).

1. No painel **Domínio personalizado**, na caixa de texto, insira o nome do seu domínio personalizado, incluindo o subdomínio.  
   Não inclua *asverify*. Por exemplo, se seu domínio está *contoso.com* e é o alias de subdomínio *www*, insira **www\.contoso.com**. Se o seu subdomínio for *photos*, digite **photos.contoso.com**.

1. Selecione a caixa de seleção **Usar validação de CNAME indireta**.

1. Para registrar seu domínio personalizado, selecione **Salvar**.  
   Se o registro for bem-sucedido, o portal notificará que sua conta de armazenamento foi atualizada com sucesso. Seu domínio personalizado foi verificado pelo Azure, mas o tráfego para seu domínio ainda não está sendo encaminhado para sua conta de armazenamento.

1. Volte ao site do provedor de DNS e crie outro registro CNAME que mapeie seu subdomínio para o ponto de extremidade do serviço de blob.  
   Por exemplo, especifique o subdomínio como *www* ou *fotos* (sem o *asverify*) e especifique o nome do host como *\<mystorageaccount>.blob. core.windows.net*, onde *mystorageaccount* é o nome da sua conta de armazenamento. Com essa etapa, o registro do seu domínio personalizado está concluído.

1. Por fim, você pode excluir o registro CNAME recém-criado que contém o subdomínio *asverify*, que era necessário apenas como uma etapa intermediária.

Depois que seu novo registro CNAME for propagado pelo DNS, se os usuários tiverem as permissões apropriadas, eles poderão visualizar os dados de blob usando seu domínio personalizado.

## <a name="test-your-custom-domain"></a>Testar seu domínio personalizado

Para confirmar que seu domínio personalizado está mapeado para o ponto de extremidade do serviço de blob, crie um blob em um contêiner público em sua conta de armazenamento. Em seguida, em um navegador da Web, acesse o blob usando um URI no seguinte formato: `http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Por exemplo, para acessar um formulário da web no contêiner *myforms* no subdomínio personalizado *photos.contoso.com*, você pode usar o seguinte URI: `http://photos.contoso.com/myforms/applicationform.htm`

## <a name="deregister-a-custom-domain"></a>Cancelar o registro de um domínio personalizado

Para cancelar o registro de um domínio personalizado para seu ponto de extremidade do Armazenamento de Blobs, use um dos procedimentos a seguir.

### <a name="azure-portal"></a>Portal do Azure

Para remover a configuração do domínio personalizado, faça o seguinte:

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.

1. No painel de menu, em **Serviço de Blob**, selecione **Domínio personalizado**.  
   O painel **Domínio personalizado** é aberto.

1. Limpe o conteúdo da caixa de texto que contém seu nome de domínio personalizado.

1. Selecione o botão **Salvar**.

Depois que o domínio personalizado for removido com sucesso, você verá uma notificação do portal informando que sua conta de armazenamento foi atualizada com sucesso.

### <a name="azure-cli"></a>CLI do Azure

Para remover um registro de domínio personalizado, use o comando da CLI [az storage account](https://docs.microsoft.com/cli/azure/storage/account) e, em seguida, especifique uma cadeia vazia (`""`) para o valor do argumento `--custom-domain`.

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

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para remover um registro de domínio personalizado, use o cmdlet [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) do PowerShell e especifique uma sequência vazia (`""`) para o valor do argumento `-CustomDomainName`.

* Formato do comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Exemplo de comando:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```

## <a name="next-steps"></a>Próximas etapas
* [Mapear o domínio personalizado para o ponto de extremidade de uma CDN (Rede de Distribuição de Conteúdo) do Azure](../../cdn/cdn-map-content-to-custom-domain.md)
* [Use o CDN do Azure para acessar blobs usando domínios personalizados em HTTPS](storage-https-custom-domain-cdn.md)
* [Hospedagem de site estático no armazenamento de BLOBs do Azure (visualização)](storage-blob-static-website.md)
