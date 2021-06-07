---
title: Adicionar e gerenciar certificados TLS/SSL
description: Crie um certificado gratuito, importe um certificado do Serviço de Aplicativo, importe um certificado do Key Vault ou compre um certificado do Serviço de Aplicativo no Serviço de Aplicativo do Azure.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 03/02/2021
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 99dc8cb2acf06faae16df6d3a48c4d38b1be46d8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577777"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>Adicionar um certificado TLS/SSL ao Serviço de Aplicativo do Azure

O [Serviço de Aplicativo do Azure](overview.md) fornece um serviço de hospedagem na Web altamente escalonável e com aplicação automática de patches. Este artigo mostra como criar, carregar ou importar um certificado privado ou público no Serviço de Aplicativo. 

Depois que o certificado for adicionado ao aplicativo Serviço de Aplicativo ou [aplicativo de funções](../azure-functions/index.yml), você poderá [proteger um nome DNS personalizado com ele](configure-ssl-bindings.md) ou [usá-lo em seu código do aplicativo](configure-ssl-certificate-in-code.md).

> [!NOTE]
> Um certificado carregado em um aplicativo é armazenado em uma unidade de implantação que esteja associada ao grupo de recursos do aplicativo e à combinação de região (internamente chamada de *espaço na Web*). Isso torna o certificado acessível a outros aplicativos na mesma combinação de grupo de recursos e região. 

A tabela a seguir lista as opções disponíveis para adição de certificados no Serviço de Aplicativo:

|Opção|Descrição|
|-|-|
| Criar um Certificado gratuito gerenciado pelo Serviço de Aplicativo (versão prévia) | Um certificado privado sem custo e fácil de usar se você precisar proteger seu [domínio personalizado](app-service-web-tutorial-custom-domain.md) no Serviço de Aplicativo. |
| Comprar um certificado do Serviço de Aplicativo | Um certificado privado gerenciado pelo Azure. Ele combina a simplicidade do gerenciamento automatizado de certificado e a flexibilidade das opções de renovação e exportação. |
| Importar um certificado do Key Vault | Útil se você usar o [Azure Key Vault](../key-vault/index.yml) para gerenciar seus [certificados PKCS12](https://wikipedia.org/wiki/PKCS_12). Consulte [Requisitos do certificado privado](#private-certificate-requirements). |
| Carregar um certificado privado | Se você já tiver um certificado privado de um provedor de terceiros, poderá carregá-lo. Consulte [Requisitos do certificado privado](#private-certificate-requirements). |
| Carregar um certificado público | Os certificados públicos não são usados para proteger domínios personalizados, mas você pode carregá-los em seu código se precisar que eles acessem recursos remotos. |

## <a name="prerequisites"></a>Pré-requisitos

- [Crie um aplicativo do Serviço de Aplicativo](./index.yml).
- Para um certificado privado, verifique se ele atende a todos os [requisitos do Serviço de Aplicativo](#private-certificate-requirements).
- **Apenas certificado gratuito**:
    - Mapeie o domínio para o qual você deseja um certificado para o Serviço de Aplicativo. Para obter informações, confira [Tutorial: Mapear um nome DNS personalizado existente para o Serviço de Aplicativo do Azure](app-service-web-tutorial-custom-domain.md).
    - Para um domínio raiz (como contoso.com), verifique se seu aplicativo não tem nenhuma [restrição de IP](app-service-ip-restrictions.md) configurada. A criação de certificado e a renovação periódica dele para um domínio raiz dependem da capacidade de seu aplicativo ser acessado pela Internet.

## <a name="private-certificate-requirements"></a>Requisitos do certificado privado

O [Certificado gratuito gerenciado pelo Serviço de Aplicativo](#create-a-free-managed-certificate-preview) e o [certificado do Serviço de Aplicativo](#import-an-app-service-certificate) já atende aos requisitos do Serviço de Aplicativo. Se você optar por carregar ou importar um certificado privado no Serviço de Aplicativo, o certificado deverá atender aos seguintes requisitos:

* Exportado como um [arquivo PFX protegido por senha](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions), criptografado usando DES triplo.
* Conter chave privada com pelo menos 2.048 bits de extensão
* Conter todos os certificados intermediários na cadeia de certificados

Para proteger um domínio personalizado em uma associação TLS, o certificado tem requisitos adicionais:

* Contém um [Uso estendido de chave](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) para autenticação do servidor (OID = 1.3.6.1.5.5.7.3.1)
* Assinado por uma autoridade de certificado confiável

> [!NOTE]
> Os **certificados ECC (Criptografia de Curva Elíptica)** podem funcionar com o Serviço de Aplicativo, mas não são abordados neste artigo. Trabalhe com sua autoridade de certificação seguindo exatamente estas etapas para criar certificados ECC.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-managed-certificate-preview"></a>Criar um certificado gerenciado gratuito (versão prévia)

> [!NOTE]
> Antes de criar um certificado gerenciado gratuito, verifique se [você atendeu aos pré-requisitos](#prerequisites) para seu aplicativo.

O Certificado gratuito gerenciado pelo Serviço de Aplicativo é uma solução imediata para proteção de seu nome DNS personalizado no Serviço de Aplicativo. É um certificado TLS/SSL totalmente funcional gerenciado pelo Serviço de Aplicativo e renovado automaticamente. O certificado gratuito apresenta as seguintes limitações:

- Não oferece suporte a certificados curinga.
- Não pode ser exportado.
- Não tem suporte no ASE (Ambiente do Serviço de Aplicativo).
- Não tem suporte com domínios raiz integrados ao Gerenciador de Tráfego.

> [!NOTE]
> O certificado gratuito é emitido pela DigiCert. Para alguns domínios de nível superior, é necessário permitir explicitamente a DigiCert como um emissor do certificado criando um [registro de domínio CAA](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) com o valor `0 issue digicert.com`.
> 

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, no menu à esquerda, selecione **Serviços de Aplicativos** >  **\<app-name>** .

No painel de navegação à esquerda em seu aplicativo, selecione **Configurações de TLS/SSL** > **Certificados de Chave Privada (.pfx)**  > **Criar Certificado Gerenciado do Serviço de Aplicativo**.

![Criar certificado gratuito no Serviço de Aplicativo](./media/configure-ssl-certificate/create-free-cert.png)

Selecione o domínio personalizado para criar um certificado gratuito e selecione **Criar**. Só é possível criar apenas um certificado para cada domínio personalizado compatível.

Quando a operação for concluída, você verá o certificado na lista **Certificados de Chave Privada**.

![Criação de certificado gratuito concluída](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Para proteger um domínio personalizado com esse certificado, ainda é necessário criar uma associação de certificado. Execute as etapas em [Criar associação](configure-ssl-bindings.md#create-binding).
>

## <a name="import-an-app-service-certificate"></a>Importar um Certificado do Serviço de Aplicativo

Se você comprar um Certificado do Serviço de Aplicativo no Azure, o Azure será responsável por:

- Cuidar do processo de compra no GoDaddy.
- Executar a verificação de domínio do certificado.
- Manter o certificado no [Azure Key Vault](../key-vault/general/overview.md).
- Gerenciar a renovação do certificado (consulte [Renovar certificado](#renew-certificate)).
- Sincronizar automaticamente o certificado com as cópias importadas nos aplicativos do Serviço de Aplicativo.

Para comprar um certificado do Serviço de Aplicativo, acesse [Iniciar o pedido de certificado](#start-certificate-order).

Se você já tiver um certificado funcional do Serviço de Aplicativo, poderá:

- [Importar o certificado no Serviço de Aplicativo](#import-certificate-into-app-service).
- [Gerenciar o certificado](#manage-app-service-certificates), por exemplo, renovar, rechavear e exportá-lo.
> [!NOTE]
> Não há suporte para os Certificados do Serviço de Aplicativo nas Nuvens Nacionais do Azure no momento.

### <a name="start-certificate-order"></a>Iniciar o pedido de certificado

Inicie um pedido de certificado de Serviço de Aplicativo na <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">página de criação de Certificado do Serviço de Aplicativo</a>.

![Iniciar a compra do certificado do Serviço de Aplicativo](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Use a tabela a seguir para ajudá-lo a configurar o certificado. Ao terminar, clique em **Criar**.

| Configuração | Descrição |
|-|-|
| Nome | Um nome amigável para o seu certificado de Serviço de Aplicativo. |
| Nome do Host do Domínio Raiz | Especifique o domínio raiz aqui. O certificado emitido protege *ambos*, o domínio raiz e o subdomínio `www`. No certificado emitido, o campo Nome Comum contém o domínio raiz, e o campo Nome Alternativo da Entidade contém o domínio `www`. Para proteger apenas um subdomínio qualquer, especifique aqui o nome de domínio totalmente qualificado do subdomínio (por exemplo, `mysubdomain.contoso.com`).|
| Subscription | A assinatura que conterá o certificado. |
| Resource group | O grupo de recursos que conterá o certificado. Você pode usar um novo grupo de recursos ou selecionar o mesmo grupo de recursos que seu aplicativo de Serviço de Aplicativo, por exemplo. |
| Certificado SKU | Determina o tipo de certificado para criar, se um certificado padrão ou uma [certificado curinga](https://wikipedia.org/wiki/Wildcard_certificate). |
| Termos legais | Clique para confirmar que você concorda com os termos legais. Os certificados são obtidos no GoDaddy. |

> [!NOTE]
> Os Certificados do Serviço de Aplicativo adquiridos do Azure são emitidos pelo GoDaddy. Para alguns domínios de nível superior, é necessário permitir explicitamente o GoDaddy como um emissor do certificado criando um [registro de domínio CAA](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) com o valor: `0 issue godaddy.com`
> 

### <a name="store-in-azure-key-vault"></a>Armazenar no Azure Key Vault

Quando o processo de compra do certificado tiver sido concluído, haverá mais algumas etapas a serem concluídas antes de começar a usar esse certificado. 

Selecione o certificado na página [Certificados do Serviço de Aplicativo](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e, em seguida, clique em **Configuração do Certificado** > **Etapa 1: armazenar**.

![Configurar o armazenamento do Key Vault para o certificado do Serviço de Aplicativo](./media/configure-ssl-certificate/configure-key-vault.png)

O [Cofre da Chave](../key-vault/general/overview.md) do Azure ajuda a proteger chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. É o armazenamento de escolha para certificados de Serviço de Aplicativo.

Na página **Status do Key Vault**, clique em **Repositório do Key Vault** para criar um novo cofre ou escolher um cofre existente. Se você optar por criar um novo cofre, use a tabela a seguir para ajudá-lo a configurar o cofre e clique em Criar. Crie o Key Vault dentro da mesma assinatura e do mesmo grupo de recursos do aplicativo Serviço de Aplicativo.

| Configuração | Descrição |
|-|-|
| Nome | Um nome exclusivo que consiste em caracteres alfanuméricos e traços. |
| Resource group | Como recomendação, selecione o mesmo grupo de recursos do seu certificado de Serviço de Aplicativo. |
| Location | Selecione o mesmo local que o aplicativo de Serviço de Aplicativo. |
| Tipo de preço | Para obter mais informações, confira [Detalhes de preços do Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). |
| Políticas de acesso| Define os aplicativos e o acesso permitido aos recursos do cofre. É possível configurá-lo mais tarde seguindo as etapas em [Atribuir uma política de acesso do Key Vault](../key-vault/general/assign-access-policy-portal.md). |
| Acesso à Rede Virtual | Restringir o acesso do cofre a determinadas redes virtuais do Azure. Você pode configurá-lo mais tarde seguindo as etapas em [Configurar o Redes Virtuais e Firewall do Azure Key Vault](../key-vault/general/network-security.md) |

Depois de selecionar o cofre, feche a página **Repositório do Key Vault**. A opção **Etapa 1: Armazenar** deverá mostrar uma marca de seleção verde para indicar êxito. Mantenha a página aberta para a próxima etapa.

### <a name="verify-domain-ownership"></a>Verificar a propriedade de domínio

Na mesma página **Configuração do Certificado** usada na última etapa, clique em **Etapa 2: verificar**.

![Verificar o domínio do certificado do Serviço de Aplicativo](./media/configure-ssl-certificate/verify-domain.png)

Selecione **Verificação do Serviço de Aplicativo**. Uma vez que você já mapeou o domínio ao seu aplicativo Web (veja [Pré-requisitos](#prerequisites)), ele já foi verificado. Basta clicar em **Verificar** para concluir esta etapa. Clique no botão **Atualizar** até que a mensagem **O Certificado tem Domínio Verificado** seja exibida.

> [!NOTE]
> Há suporte para quatro tipos de métodos de verificação de domínio: 
> 
> - **Serviço de Aplicativo** – a opção mais conveniente quando o domínio já está mapeado para um aplicativo do Serviço de Aplicativo na mesma assinatura. Ela tira proveito do fato de que o aplicativo de Serviço de Aplicativo já verificou a propriedade de domínio.
> - **Domínio** – verifique um [domínio do Serviço de Aplicativo que você adquiriu do Azure](manage-custom-dns-buy-domain.md). O Azure adiciona automaticamente a verificação do registro TXT para você e conclui o processo.
> - **Email** – verifique o domínio enviando um email para o administrador de domínio. As instruções são fornecidas quando você seleciona a opção.
> - **Manual** – verifique o domínio usando uma página HTML (apenas certificado **Standard**) ou um registro TXT do DNS. As instruções são fornecidas quando você seleciona a opção.

### <a name="import-certificate-into-app-service"></a>Importar o certificado para o Serviço de Aplicativo

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, no menu à esquerda, selecione **Serviços de Aplicativos** >  **\<app-name>** .

No painel de navegação à esquerda de seu aplicativo, selecione **Configurações de TLS/SSL** > **Certificados de Chave Privada (.pfx)**  > **Importar Certificado do Serviço de Aplicativo**.

![Importar certificado do Serviço de Aplicativo no Serviço de Aplicativo](./media/configure-ssl-certificate/import-app-service-cert.png)

Selecione o certificado que você acabou de comprar e selecione **OK**.

Quando a operação for concluída, você verá o certificado na lista **Certificados de Chave Privada**.

![Importação do certificado do Serviço de Aplicativo concluída](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Para proteger um domínio personalizado com esse certificado, ainda é necessário criar uma associação de certificado. Execute as etapas em [Criar associação](configure-ssl-bindings.md#create-binding).
>

## <a name="import-a-certificate-from-key-vault"></a>Importar um certificado do Key Vault

Se você usa o Azure Key Vault para gerenciar seus certificados, pode importar um certificado PKCS12 do Key Vault para o Serviço de Aplicativo, contanto que ele [atenda aos requisitos](#private-certificate-requirements).

### <a name="authorize-app-service-to-read-from-the-vault"></a>Autorizar o Serviço de Aplicativo a ler no cofre
Por padrão, o provedor de recursos do Serviço de Aplicativo não tem acesso ao Key Vault. Para usar um Key Vault para uma implantação de certificado, você precisa [autorizar o acesso de leitura do provedor de recursos ao Key Vault](../key-vault/general/assign-access-policy-cli.md). 

`abfa0a7c-a6b6-4736-8310-5855508787cd` é o nome da entidade de serviço do provedor de recursos para o Serviço de Aplicativo e é igual para todas as assinaturas do Azure. Para o ambiente de nuvem do Azure Government, use `6a02c803-dafd-4136-b4c3-5a6f318b4714` como o nome da entidade de serviço do provedor de recursos.

### <a name="import-a-certificate-from-your-vault-to-your-app"></a>Importar um certificado de seu cofre para seu aplicativo

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, no menu à esquerda, selecione **Serviços de Aplicativos** >  **\<app-name>** .

No painel de navegação à esquerda de seu aplicativo, selecione **Configurações de TLS/SSL** > **Certificados de Chave Privada (.pfx)**  > **Importar Certificado do Key Vault**.

![Importar certificado do Key Vault no Serviço de Aplicativo](./media/configure-ssl-certificate/import-key-vault-cert.png)

Use a tabela a seguir para selecionar o certificado.

| Configuração | Descrição |
|-|-|
| Subscription | A assinatura à qual o Key Vault pertence. |
| Key Vault | O cofre com o certificado que você quer importar. |
| Certificado | Selecione na lista de certificados PKCS12 no cofre. Todos os certificados PKCS12 no cofre são listados com suas impressões digitais, mas nem todos têm suporte no Serviço de Aplicativo. |

Quando a operação for concluída, você verá o certificado na lista **Certificados de Chave Privada**. Se a importação falhar com um erro, o certificado não atenderá aos [requisitos do Serviço de Aplicativo](#private-certificate-requirements).

![Importação do certificado do Key Vault concluída](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!NOTE]
> Se você atualizar seu certificado no Key Vault com um novo certificado, o Serviço de Aplicativo sincronizará automaticamente seu certificado dentro de 48 horas.

> [!IMPORTANT] 
> Para proteger um domínio personalizado com esse certificado, ainda é necessário criar uma associação de certificado. Execute as etapas em [Criar associação](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-private-certificate"></a>Carregar um certificado privado

Depois de obter um certificado do provedor de certificados, siga as etapas desta seção para prepará-lo para o Serviço de Aplicativo.

### <a name="merge-intermediate-certificates"></a>Mesclar certificados intermediários

Se a autoridade de certificação fornecer vários certificados na cadeia de certificados, você precisará mesclar os certificados na ordem.

Para fazer isso, abra cada certificado recebido em um editor de texto.

Crie um arquivo para o certificado mesclado, chamado _mergedcertificate.crt_. Em um editor de texto, copie o conteúdo de cada certificado para esse arquivo. A ordem de seus certificados deve seguir a ordem na cadeia de certificados, começando com o seu certificado e terminando com o certificado raiz. Ela se parece com o seguinte exemplo:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Exportar o certificado para PFX

Exporte o certificado TLS/SSL mesclado com a chave privada com a qual a solicitação de certificado foi gerada.

Se você gerou a solicitação de certificado usando o OpenSSL, isso significa que você criou um arquivo de chave privada. Para exportar o certificado para PFX, execute o comando a seguir. Substitua os espaços reservados _&lt;private-key-file>_ e _&lt;merged-certificate-file>_ pelos caminhos para a sua chave privada e o arquivo de certificado mesclado.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Quando solicitado, defina uma senha de exportação. Você usará essa senha quando carregar o certificado TLS/SSL para o Serviço de Aplicativo posteriormente.

Se você usou o IIS ou o _Certreq.exe_ para gerar a solicitação de certificado, instale o certificado no computador local e, em seguida, [exporte o certificado para PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="upload-certificate-to-app-service"></a>Carregar certificado no Serviço de Aplicativo

Agora você está pronto para carregar o certificado no Serviço de Aplicativo.

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, no menu à esquerda, selecione **Serviços de Aplicativos** >  **\<app-name>** .

No painel de navegação à esquerda de seu aplicativo, selecione **Configurações de TLS/SSL** > **Certificados de Chave Privada (.pfx)**  > **Carregar Certificado**.

![Carregar certificado privado no Serviço de Aplicativo](./media/configure-ssl-certificate/upload-private-cert.png)

Em **Arquivo de Certificado PFX**, selecione o arquivo PFX. Em **Senha do certificado**, digite a senha que você criou ao exportar o arquivo PFX. Ao terminar, clique em **Carregar**. 

Quando a operação for concluída, você verá o certificado na lista **Certificados de Chave Privada**.

![Carregamento do certificado concluído](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Para proteger um domínio personalizado com esse certificado, ainda é necessário criar uma associação de certificado. Execute as etapas em [Criar associação](configure-ssl-bindings.md#create-binding).
>

## <a name="upload-a-public-certificate"></a>Carregar um certificado público

Há suporte para certificados públicos no formato *.cer*. 

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, no menu à esquerda, selecione **Serviços de Aplicativos** >  **\<app-name>** .

No painel de navegação à esquerda de seu aplicativo, clique em **Configurações de TLS/SSL** > **Certificados Públicos (.cer)**  > **Carregar o Certificado de Chave Pública**.

Em **Nome**, digite um nome para o certificado. Em **Arquivo de Certificado CER**, selecione o arquivo CER.

Clique em **Carregar**.

![Carregar certificado público no Serviço de Aplicativo](./media/configure-ssl-certificate/upload-public-cert.png)

Após o carregamento do certificado, copie a impressão digital do certificado e consulte [Tornar o certificado acessível](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>Gerenciar certificados do Serviço de Aplicativo

Esta seção mostra como gerenciar um certificado do Serviço de Aplicativo comprado em [Importar um certificado do Serviço de Aplicativo](#import-an-app-service-certificate).

- [Rechavear o certificado](#rekey-certificate)
- [Renovar o certificado](#renew-certificate)
- [Exportar o certificado](#export-certificate)
- [Excluir o certificado](#delete-certificate)

### <a name="rekey-certificate"></a>Rechavear o certificado

Se você acha que a chave privada do seu certificado está comprometida, pode rechaveá-lo. Selecione o certificado na página [Certificados do Serviço de Aplicativo](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e, em seguida, selecione **Rechaveamento e Sincronização** na navegação à esquerda.

Clique em **Rechavear** para iniciar o processo. Esse processo pode demorar de um a 10 minutos para ser concluído.

![Rechavear um certificado do Serviço de Aplicativo](./media/configure-ssl-certificate/rekey-app-service-cert.png)

A criação de uma nova chave para o certificado causará a emissão de um novo certificado pela autoridade de certificação.

Após a conclusão da operação de rechaveamento, clique em **Sincronizar**. A operação de sincronização atualiza automaticamente as associações de nome de host do certificado no Serviço de Aplicativo sem causar tempo de inatividade em seus aplicativos.

> [!NOTE]
> Se você não clicar em **Sincronizar**, o Serviço de Aplicativo sincronizará automaticamente seu certificado dentro de 48 horas.

### <a name="renew-certificate"></a>Renovar certificado

Para ativar a renovação automática do seu certificado a qualquer momento, selecione o certificado na página [Certificados do Serviço de Aplicativo](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e clique em **Configurações de Renovação Automática** no painel de navegação à esquerda. Por padrão, os certificados do Serviço de Aplicativo têm um período de validade de um ano.

Selecione **Ativado** e clique em **Salvar**. A renovação dos certificados poderá iniciar automaticamente 30 dias antes da expiração deles se você tiver ativado a renovação automática.

![Renovar automaticamente o certificado do Serviço de Aplicativo](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Para renovar manualmente o certificado, clique em **Renovação Manual**. Você pode solicitar renovar manualmente o certificado de 60 dias antes da expiração.

Após a conclusão da operação de renovação, clique em **Sincronizar**. A operação de sincronização atualiza automaticamente as associações de nome de host do certificado no Serviço de Aplicativo sem causar tempo de inatividade em seus aplicativos.

> [!NOTE]
> Se você não clicar em **Sincronizar**, o Serviço de Aplicativo sincronizará automaticamente seu certificado dentro de 48 horas.

### <a name="export-certificate"></a>Exportar o certificado

Como um Certificado do Serviço de Aplicativo é um [segredo do Key Vault](../key-vault/general/about-keys-secrets-certificates.md), você pode exportar uma cópia em PFX dele e usá-la para outros serviços do Azure ou fora do Azure.

Para exportar o Certificado do Serviço de Aplicativo como um arquivo PFX, execute os seguintes comandos no [Cloud Shell](https://shell.azure.com). Você também poderá executá-lo localmente se tiver [instalado a CLI do Azure](/cli/azure/install-azure-cli). Substitua os espaços reservados pelos nomes que você usou ao [criar o certificado do Serviço de Aplicativo](#start-certificate-order).

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

O arquivo *appservicecertificate.pfx* baixado é um arquivo PKCS12 bruto que contém os certificados públicos e privados. Em cada aviso, use uma cadeia de caracteres vazia para a senha de importação e a frase secreta PEM.

### <a name="delete-certificate"></a>Excluir Certificado 

A exclusão de um certificado do Serviço de Aplicativo é final e irreversível. A exclusão de um recurso de Certificado do Serviço de Aplicativo resulta na revogação do certificado. Qualquer associação no Serviço de Aplicativo com esse certificado ficará inválida. Para evitar a exclusão acidental, o Azure coloca um bloqueio no certificado. Para excluir um certificado do Serviço de Aplicativo, primeiro remova o bloqueio de exclusão no certificado.

Selecione o certificado na página [Certificados do Serviço de Aplicativo](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) e, em seguida, selecione **Bloqueios** na navegação à esquerda.

Localize o bloqueio em seu certificado com o tipo de bloqueio **Exclusão**. À direita, selecione **Excluir**.

![Excluir bloqueio do certificado do Serviço de Aplicativo](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Agora você pode excluir o certificado do Serviço de Aplicativo. Na navegação à esquerda, selecione **Visão Geral** > **Excluir**. Na caixa de diálogo de confirmação, digite o nome do certificado e selecione **OK**.

## <a name="automate-with-scripts"></a>Automatizar com scripts

### <a name="azure-cli"></a>CLI do Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Mais recursos

* [Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure](configure-ssl-bindings.md)
* [Impor HTTPS](configure-ssl-bindings.md#enforce-https)
* [Impor o TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Usar um certificado TLS/SSL no seu código no Serviço de Aplicativo do Azure](configure-ssl-certificate-in-code.md)
* [Perguntas frequentes: Certificados do Serviço de Aplicativo](./faq-configuration-and-management.md)
