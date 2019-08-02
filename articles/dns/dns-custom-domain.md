---
title: Integrar o DNS do Azure aos recursos do Azure
description: Saiba como usar o DNS do Azure para fornecer o DNS para os seus recursos do Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 7/13/2019
ms.author: victorh
ms.openlocfilehash: 051aabed758f80208549cf64bf5d74b1fecfbe75
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854155"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Usar o DNS do Azure para fornecer as configurações de domínio personalizadas para um serviço do Azure

O DNS do Azure fornece o DNS para um domínio personalizado para qualquer um dos recursos do Azure que dão suporte a domínios personalizados ou que têm um FQDN (nome de domínio totalmente qualificado). Um exemplo é que você tem um aplicativo Web do Azure e deseja que os usuários o acessem usando o contoso.com ou o contoso.com\.da Web como um FQDN. Este artigo o orienta na configuração do serviço do Azure com o DNS do Azure para usar domínios personalizados.

## <a name="prerequisites"></a>Pré-requisitos

Para usar o DNS do Azure para seu domínio personalizado, primeiro você deve delegar seu domínio ao DNS do Azure. Acesse [Delegar um domínio DNS do Azure](./dns-delegate-domain-azure-dns.md) para obter instruções sobre como configurar os servidores de nome para delegação. Depois que seu domínio for delegado para a zona DNS do Azure, é possível configurar os registros DNS necessários.

Você pode configurar um domínio intuitivo ou personalizado para os [Aplicativos de Funções do Azure](#azure-function-app), [Endereços IP Públicos](#public-ip-address), [Aplicativos Web do Serviço de Aplicativo](#app-service-web-apps), [Armazenamento de Blobs](#blob-storage) e [CDN do Azure](#azure-cdn).

## <a name="azure-function-app"></a>Aplicativo de Funções do Azure

Para configurar um domínio personalizado para aplicativos de funções do Azure, um registro CNAME é criado, bem como o aplicativo de configuração em si.
 
Navegue para **Aplicativo de Funções** e selecione seu aplicativo de funções. Clique em **Recursos da plataforma** e, em **Rede**, clique em **Domínios personalizados**.

![folha de aplicativo de funções](./media/dns-custom-domain/functionapp.png)

Anote a url atual na folha **Domínios personalizados**, esse endereço é usado como o alias para o registro DNS criado.

![folha de domínio personalizado](./media/dns-custom-domain/functionshostname.png)

Navegue para a sua zona DNS e clique em **+ Conjunto de registros**. Preencha as seguintes informações na folha **Adicionar conjunto de registros** e clique em **OK** para criá-lo.

|Propriedade  |Valor  |DESCRIÇÃO  |
|---------|---------|---------|
|Nome     | myfunctionapp        | Esse valor junto com o rótulo de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Use um registro CNAME que esteja usando um alias.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade de TTL     | Horas        | Horas são usadas como a medida de tempo         |
|Alias     | adatumfunction.azurewebsites.net        | Neste exemplo, o nome DNS para o qual você está criando o alias é o nome DNS adatumfunction.azurewebsites.net fornecido por padrão para o aplicativo de funções.        |

Navegue de volta para o aplicativo de funções, clique em **Recursos da plataforma** e, em **Rede**, clique em **Domínios personalizados**; em seguida, em **Nomes do Host Personalizados**, clique em **+ Adicionar nome do host**.

Na folha **Adicionar nome de host**, insira o registro CNAME no campo de texto **hostname** e clique em **Validar**. Se o registro for encontrado, o botão **Adicionar nome do host** será exibido. Clique em **Adicionar nome de host** para adicionar o alias.

![aplicativos de função adicionam a folha de nome do host](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Endereço IP público

Para configurar um domínio personalizado para serviços que usam um recurso de endereço IP público, como gateway de aplicativo, Load Balancer, serviço de nuvem, VMs do Gerenciador de recursos e VMs clássicas, um registro A é usado.

Navegue até **Rede** > **Endereço IP público**, selecione o recurso IP Público e clique em **Configuração**. Marque o endereço IP mostrado.

![folha de ip público](./media/dns-custom-domain/publicip.png)

Navegue para a sua zona DNS e clique em **+ Conjunto de registros**. Preencha as seguintes informações na folha **Adicionar conjunto de registros** e clique em **OK** para criá-lo.


|Propriedade  |Valor  |DESCRIÇÃO  |
|---------|---------|---------|
|NOME     | mywebserver        | Esse valor junto com o rótulo de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | O        | Use um registro A, uma vez que o recurso é um endereço IP.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade de TTL     | Horas        | Horas são usadas como a medida de tempo         |
|Endereço IP     | `<your ip address>`       | O endereço IP público.|

![criar um registro A](./media/dns-custom-domain/arecord.png)

Depois que o registro A for criado, execute `nslookup` para validar a resolução do registro.

![pesquisa de dns do ip público](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>Serviço de Aplicativo (Aplicativos Web)

As etapas a seguir o conduzem pela configuração de um domínio personalizado para um aplicativo Web do serviço de aplicativo.

Navegue para **Serviço de Aplicativo** e selecione o recurso para o qual você está configurando um nome de domínio personalizado e clique em **Domínios personalizados**.

Anote a url atual na folha **Domínios personalizados**, esse endereço é usado como o alias para o registro DNS criado.

![folha de domínios personalizados](./media/dns-custom-domain/url.png)

Navegue para a sua zona DNS e clique em **+ Conjunto de registros**. Preencha as seguintes informações na folha **Adicionar conjunto de registros** e clique em **OK** para criá-lo.


|Propriedade  |Valor  |DESCRIÇÃO  |
|---------|---------|---------|
|Nome     | mywebserver        | Esse valor junto com o rótulo de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Use um registro CNAME que esteja usando um alias. Se o recurso tiver usado um endereço IP, um registro deverá ser usado.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade de TTL     | Horas        | Horas são usadas como a medida de tempo         |
|Alias     | webserver.azurewebsites.net        | Neste exemplo, o nome DNS para o qual você está criando o alias é o nome DNS webserver.azurewebsites.net fornecido por padrão para o aplicativo Web.        |


![criar um registro CNAME](./media/dns-custom-domain/createcnamerecord.png)

Navegue de volta para o serviço de aplicativo que está configurado para o nome de domínio personalizado. Clique em **Domínios personalizados**, em seguida, clique em **Nomes de host**. Para adicionar o registro CNAME que você criou, clique em **+ Adicionar nome de host**.

![figura 1](./media/dns-custom-domain/figure1.png)

Quando o processo estiver concluído, execute **nslookup** para validar que a resolução de nomes está funcionando.

![figura 1](./media/dns-custom-domain/finalnslookup.png)

Para saber mais sobre como mapear um domínio personalizado para o Serviço de Aplicativo, acesse [Mapear um nome DNS personalizado existente para Aplicativos Web do Azure](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Para saber como migrar um nome DNS ativo, consulte [migrar um nome DNS ativo para Azure app serviço](../app-service/manage-custom-dns-migrate-domain.md).

Se você precisar comprar um domínio personalizado, acesse [Comprar um nome de domínio personalizado para aplicativos Web do Azure](../app-service/manage-custom-dns-buy-domain.md) para saber mais sobre domínios do Serviço de Aplicativo.

## <a name="blob-storage"></a>Armazenamento de blob

As etapas a seguir o conduzem pela configuração de um registro CNAME para uma conta de armazenamento de blobs usando o método asverify. Esse método garante que não haja tempo de inatividade.

Navegue até **Armazenamento** > **Contas de Armazenamento**, selecione sua conta de armazenamento e clique em **Domínio personalizado**. Marque o FQDN na etapa 2. Esse valor é usado para criar o primeiro registro CNAME

![domínio personalizado de armazenamento de blobs](./media/dns-custom-domain/blobcustomdomain.png)

Navegue para a sua zona DNS e clique em **+ Conjunto de registros**. Preencha as seguintes informações na folha **Adicionar conjunto de registros** e clique em **OK** para criá-lo.


|Propriedade  |Valor  |DESCRIÇÃO  |
|---------|---------|---------|
|Nome     | asverify.mystorageaccount        | Esse valor junto com o rótulo de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Use um registro CNAME que esteja usando um alias.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade de TTL     | Horas        | Horas são usadas como a medida de tempo         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Neste exemplo, o nome DNS para o qual você está criando o alias é o nome DNS asverify.adatumfunctiona9ed.blob.core.windows.net fornecido por padrão para a conta de armazenamento.        |

Navegue de volta para sua conta de armazenamento clicando em **Armazenamento** > **Contas de Armazenamento**, selecione sua conta de armazenamento e clique em **Domínio personalizado**. Digite o alias criado sem o prefixo asverify na caixa de texto, marque **Usar validação indireta de CNAME e, em seguida, clique em **Salvar**. Depois que essa etapa estiver concluída, volte para a zona DNS e crie um registro CNAME sem o prefixo asverify.  Depois desse ponto, você pode excluir com segurança o registro CNAME com o prefixo cdnverify.

![domínio personalizado de armazenamento de blobs](./media/dns-custom-domain/indirectvalidate.png)

Valide a resolução de DNS, executando `nslookup`

Para saber mais sobre como mapear um domínio personalizado para um ponto de extremidade de armazenamento de blobs, acesse [Configurar um nome de domínio personalizado para seu ponto de extremidade de armazenamento de blobs](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>CDN do Azure

As etapas a seguir o conduzem pela configuração de um registro CNAME para um ponto de extremidade CDN usando o método cdnverify. Esse método garante que não haja tempo de inatividade.

Navegue para **Rede** > **Perfis CDN**, selecione seu perfil CDN.

Selecione o ponto de extremidade com o qual você está trabalhando e clique em **+ Domínio personalizado**. Observe o **Nome de host do ponto de extremidade**, uma vez que esse valor é o registro para o qual o CNAME aponta.

![Domínio personalizado CDN](./media/dns-custom-domain/endpointcustomdomain.png)

Navegue para a sua zona DNS e clique em **+ Conjunto de registros**. Preencha as seguintes informações na folha **Adicionar conjunto de registros** e clique em **OK** para criá-lo.

|Propriedade  |Valor  |DESCRIÇÃO  |
|---------|---------|---------|
|Nome     | cdnverify.mycdnendpoint        | Esse valor junto com o rótulo de nome de domínio é o FQDN para o nome de domínio personalizado.        |
|Tipo     | CNAME        | Use um registro CNAME que esteja usando um alias.        |
|TTL     | 1        | 1 é usado por 1 hora        |
|Unidade de TTL     | Horas        | Horas são usadas como a medida de tempo         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Neste exemplo, o nome DNS para o qual você está criando o alias é o nome DNS cdnverify.adatumcdnendpoint.azureedge.net fornecido por padrão para a conta de armazenamento.        |

Navegue de volta para o ponto de extremidade de CDN clicando em **Rede** > **Perfis CDN** e selecione o seu perfil de CDN. Clique em **+ Domínio personalizado** e insira o alias de registro CNAME sem o prefixo cdnverify e clique em **Adicionar**.

Depois que essa etapa estiver concluída, volte para a zona DNS e crie um registro CNAME sem o prefixo cdnverify.  Depois desse ponto, você pode excluir com segurança o registro CNAME com o prefixo cdnverify. Para obter mais informações sobre CDN e como configurar um domínio personalizado sem a etapa de registro intermediária, acesse [Mapear conteúdo da CDN do Azure para um domínio personalizado](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Próximas etapas

Saiba como [Configurar DNS reverso para serviços hospedados no Azure](dns-reverse-dns-for-azure-services.md).
