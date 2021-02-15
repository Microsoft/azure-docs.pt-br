---
title: Proteger um DNS personalizado com uma associação TLS/SSL
description: Proteja o acesso HTTPS ao seu domínio personalizado criando uma associação TLS/SSL com um certificado. Melhore a segurança do seu site impondo o HTTPS ou o TLS 1.2.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 04/30/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: f7301809b3befc41110a32062d6e478c412fa56e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90981116"
---
# <a name="secure-a-custom-dns-name-with-a-tlsssl-binding-in-azure-app-service"></a>Proteger um nome DNS personalizado com uma associação TLS/SSL no Serviço de Aplicativo do Azure

Este artigo mostra como proteger o [domínio personalizado](app-service-web-tutorial-custom-domain.md) em seu [aplicativo do Serviço de Aplicativo](./index.yml) ou no [aplicativo de funções](../azure-functions/index.yml) criando uma associação de certificado. Quando tiver terminado, você poderá acessar seu aplicativo do Serviço de Aplicativo no ponto de extremidade `https://` para seu nome DNS personalizado (por exemplo, `https://www.contoso.com`). 

![Aplicativo Web com certificado TLS/SSL personalizado](./media/configure-ssl-bindings/app-with-custom-ssl.png)

A proteção de um [domínio personalizado](app-service-web-tutorial-custom-domain.md) com um certificado inclui duas etapas:

- [Adicionar um certificado particular ao Serviço de Aplicativo](configure-ssl-certificate.md) que atenda a todos os [requisitos de certificado privado](configure-ssl-certificate.md#private-certificate-requirements).
-  Criar uma associação TLS para o domínio personalizado correspondente. A segunda etapa é abordada neste artigo.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Atualizar o tipo de preço do aplicativo
> * Proteger um domínio personalizado com um certificado
> * Impor HTTPS
> * Impor o TLS 1.1/1.2
> * Automatizar o gerenciamento de TLS com scripts

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, é necessário ter:

- [Crie um aplicativo do Serviço de Aplicativo](./index.yml)
- [Mapear um nome de domínio para seu aplicativo](app-service-web-tutorial-custom-domain.md) ou [comprar e configurá-lo no Azure](manage-custom-dns-buy-domain.md)
- [Adicionar um certificado particular ao seu aplicativo](configure-ssl-certificate.md)

> [!NOTE]
> A maneira mais fácil de adicionar um certificado particular é [criar um Certificado Gerenciado do Serviço de Aplicativo gratuito](configure-ssl-certificate.md#create-a-free-certificate-preview) (versão prévia).

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Proteger um domínio personalizado

Execute as seguintes etapas:

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, no menu à esquerda, selecione **Serviços de Aplicativos** >  **\<app-name>** .

No painel de navegação à esquerda do seu aplicativo, inicie a caixa de diálogo **Associação TLS/SSL** da seguinte maneira:

- Selecione **Domínios personalizados** > **Adicionar associação**
- Selecione **Configurações de TLS/SSL** > **Adicionar associação TLS/SSL**

![Adicionar uma associação ao domínio](./media/configure-ssl-bindings/secure-domain-launch.png)

Em **domínio personalizado**, selecione o domínio personalizado para o qual você deseja adicionar uma associação.

Se seu aplicativo já tiver um certificado para o domínio personalizado selecionado, vá diretamente para [Criar associação](#create-binding). Caso contrário, continue.

### <a name="add-a-certificate-for-custom-domain"></a>Adicionar um certificado para o domínio personalizado

Se seu aplicativo não tiver nenhum certificado para o domínio personalizado selecionado, você terá duas opções:

- **Carregar um certificado PFX** – Siga o fluxo de trabalho em [Carregar um certificado particular](configure-ssl-certificate.md#upload-a-private-certificate) e, em seguida, selecione essa opção aqui.
- **Importar um certificado do Serviço de Aplicativo** – Siga o fluxo de trabalho em [Importar um certificado do Serviço de Aplicativo](configure-ssl-certificate.md#import-an-app-service-certificate) e, em seguida, selecione essa opção aqui.

> [!NOTE]
> Você também pode [Criar um certificado gratuito](configure-ssl-certificate.md#create-a-free-certificate-preview) (versão prévia) ou [Importar um certificado Key Vault](configure-ssl-certificate.md#import-a-certificate-from-key-vault), mas é preciso fazer isso separadamente e retornar à caixa de diálogo **Associação TLS/SSL**.

### <a name="create-binding"></a>Criar associação

Use a tabela a seguir para configurar a associação TLS na caixa de diálogo **Associação TLS/SSL** e, em seguida, clique em **Adicionar Associação**.

| Configuração | Descrição |
|-|-|
| Domínio personalizado | O nome de domínio ao qual adicionar a associação TLS/SSL. |
| Impressão Digital do Certificado Privado | O certificado a ser associado. |
| Tipo de TLS/SSL | <ul><li>**[SSL SNI](https://en.wikipedia.org/wiki/Server_Name_Indication)** – É possível adicionar várias associações SSL SNI. Esta opção permite que vários certificados TLS/SSL protejam vários domínios no mesmo endereço IP. A maioria dos navegadores modernos (incluindo Internet Explorer, Chrome, Firefox e Opera) é compatível com SNI (para saber mais, confira [Indicação de Nome de Servidor](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**IP SSL** – Apenas uma associação IP SSL pode ser adicionada. Esta opção permite apenas um certificado TLS/SSL para proteger um endereço IP público dedicado. Depois de configurar a associação, siga as etapas em [Remapear registros para IP SSL](#remap-records-for-ip-ssl).<br/>IP SSL é compatível apenas com o nível **Standard** ou superior. </li></ul> |

Quando a operação for concluída, o estado TLS/SSL do domínio personalizado será alterado para **Seguro**.

![Associação TLS/SSL bem-sucedida](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> Um estado **Seguro** nos **Domínios personalizados** significa que ele é protegido com um certificado, mas o Serviço de Aplicativo não verifica se o certificado é autoassinado ou expirou, por exemplo, o que também pode fazer com que os navegadores mostrem um erro ou um aviso.

## <a name="remap-records-for-ip-ssl"></a>Remapear registros para IP SSL

Caso não use IP SSL no seu aplicativo, pule para [Testar o HTTPS para o domínio personalizado](#test-https).

Há duas alterações que você potencialmente precisa fazer:

- Por padrão, o aplicativo usa um endereço IP público compartilhado. Quando você associa um certificado com IP SSL, o Serviço de Aplicativo cria um novo endereço IP dedicado para o aplicativo. Se você mapeou um registro A para o aplicativo, atualize o registro do domínio com esse novo endereço IP dedicado.

    A página **Domínio personalizado** de seu aplicativo é atualizada com o novo endereço IP dedicado. [Copie esse endereço IP](app-service-web-tutorial-custom-domain.md#info) e, em seguida, [remapeie o registro](app-service-web-tutorial-custom-domain.md#map-an-a-record) para esse novo endereço IP.

- Se você tiver uma associação SSL SNI para `<app-name>.azurewebsites.net`, [remapeie qualquer mapeamento CNAME](app-service-web-tutorial-custom-domain.md#map-a-cname-record) para apontar para `sni.<app-name>.azurewebsites.net` em vez disso (adicione o prefixo `sni`).

## <a name="test-https"></a>Testar HTTPS

Em vários navegadores, navegue até `https://<your.custom.domain>` para verificar se ele leva até seu aplicativo.

:::image type="content" source="./media/configure-ssl-bindings/app-with-custom-ssl.png" alt-text="Captura de tela mostrando um exemplo de como fazer a navegação até o domínio personalizado com a URL contoso.com realçada.":::

O código do aplicativo pode inspecionar o protocolo por meio do cabeçalho "x-appservice-proto". O cabeçalho terá um valor de `http` ou `https`. 

> [!NOTE]
> Caso seu aplicativo retorne erros de validação de certificado, você provavelmente está usando um certificado autoassinado.
>
> Se não for o caso, talvez você tenha deixado de fora certificados intermediários quando exportou o certificado para o arquivo PFX.

## <a name="prevent-ip-changes"></a>Impedir alterações de IP

Seu endereço IP de entrada pode ser alterado ao excluir uma associação, mesmo se essa associação for IP SSL. Isso é especialmente importante quando você renova um certificado que já está em uma associação IP SSL. Para evitar uma alteração no endereço IP do seu aplicativo, siga estas etapas pela ordem:

1. Carregar o novo certificado.
2. Associe o novo certificado para o domínio personalizado que você deseja sem excluir o antigo. Essa ação substitui a associação em vez de remover a antiga.
3. Exclua o certificado antigo. 

## <a name="enforce-https"></a>Impor HTTPS

Por padrão, qualquer pessoa ainda pode acessar seu aplicativo usando HTTP. Você pode redirecionar todas as solicitações HTTP para a porta HTTPS.

Na página do seu aplicativo, na navegação esquerda, selecione **Configurações SSL**. Depois, em **HTTPS somente**, selecione **Ligado**.

![Impor HTTPS](./media/configure-ssl-bindings/enforce-https.png)

Quando a operação estiver concluída, navegue até qualquer uma das URLs HTTP que aponte para seu aplicativo. Por exemplo: 

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Impor as versões do TLS

Seu aplicativo permite o [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 por padrão, que é o nível TLS recomendado segundo os padrões do setor, como [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Para impor versões diferentes do TLS, execute estas etapas:

Na página do seu aplicativo, na navegação esquerda, selecione **Configurações SSL**. Depois, em **Versão do TLS**, selecione a versão mínima desejada do TLS. Essa configuração controla somente as chamadas de entrada. 

![Impor o TLS 1.1 ou 1.2](./media/configure-ssl-bindings/enforce-tls1-2.png)

Após a conclusão da operação, seu aplicativo rejeitará todas as conexões com versões inferiores do TLS.

## <a name="handle-tls-termination"></a>Processar a terminação TLS

No Serviço de Aplicativo, a [Terminação TLS](https://wikipedia.org/wiki/TLS_termination_proxy) ocorre nos balanceadores de carga de rede de modo que todas as solicitações HTTPS cheguem ao seu aplicativo como solicitações HTTP não criptografadas. Se a lógica de aplicativo precisar verificar se as solicitações do usuário estão criptografadas ou não, inspecione o cabeçalho `X-Forwarded-Proto`.

Os guias de configuração específicos da linguagem, como o guia de [configuração do Linux Node.js](configure-language-nodejs.md#detect-https-session), mostram como detectar uma sessão HTTPS no código do aplicativo.

## <a name="automate-with-scripts"></a>Automatizar com scripts

### <a name="azure-cli"></a>CLI do Azure

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Mais recursos

* [Usar um certificado TLS/SSL no seu código no Serviço de Aplicativo do Azure](configure-ssl-certificate-in-code.md)
* [Perguntas frequentes: Certificados do Serviço de Aplicativo](./faq-configuration-and-management.md)