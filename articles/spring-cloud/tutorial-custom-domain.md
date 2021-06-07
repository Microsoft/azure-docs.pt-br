---
title: 'Tutorial: Mapear um domínio personalizado existente para o Azure Spring Cloud'
description: Como mapear um nome DNS (Sistema de Nomes de Domínio) personalizado existente para o Azure Spring Cloud
author: MikeDodaro
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7d01f3d5efeee2db5f468a0fe6217d1ff3c313a7
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375008"
---
# <a name="tutorial-map-an-existing-custom-domain-to-azure-spring-cloud"></a>Tutorial: Mapear um domínio personalizado existente para o Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

O DNS (Serviço de Nomes de Domínio) é uma técnica para armazenar nomes de nó de rede em uma rede. Este tutorial mapeia um domínio, como www.contoso.com, usando um registro CNAME. Ele protege o domínio personalizado com um certificado e mostra como impor o protocolo TLS, também conhecido como protocolo SSL. 

Os certificados criptografam o tráfego da Web. Esses certificados TLS/SSL podem ser armazenados no Azure Key Vault. 

## <a name="prerequisites"></a>Pré-requisitos
* Um aplicativo implantado no Azure Spring Cloud (confira [Início rápido: Iniciar um aplicativo existente do Azure Spring Cloud usando o portal do Azure](spring-cloud-quickstart.md) ou use um aplicativo existente).
* Um nome de domínio com acesso ao Registro DNS para um provedor de domínio, como o GoDaddy.
* Um certificado privado (ou seja, seu certificado autoassinado) de um provedor de terceiros. O certificado precisa corresponder ao domínio.
* Uma instância implantada do [Azure Key Vault](../key-vault/general/overview.md)

## <a name="keyvault-private-link-considerations"></a>Considerações sobre o Link Privado do Key Vault

Os IPs de gerenciamento do Azure Spring Cloud não fazem parte dos serviços confiáveis da Microsoft do Azure. Portanto, para permitir que o Azure Spring Cloud carregue certificados de um Key Vault protegido com as conexões de ponto de extremidade privado, você precisa adicionar os seguintes IPs ao Firewall do Azure Key Vault:

```
20.53.123.160 52.143.241.210 40.65.234.114 52.142.20.14 20.54.40.121 40.80.210.49 52.253.84.152 20.49.137.168 40.74.8.134 51.143.48.243
```

## <a name="import-certificate"></a>Importar certificado
### <a name="prepare-your-certificate-file-in-pfx-optional"></a>Preparar seu arquivo de certificado em PFX (opcional)
O Azure Key Vault dá suporte à importação do certificado privado nos formatos PEM e PFX. Se o arquivo PEM obtido do seu provedor de certificados não funcionar na seção abaixo: [Salve o certificado no Key Vault](#save-certificate-in-key-vault), siga as etapas aqui para gerar um PFX para o Azure Key Vault.

#### <a name="merge-intermediate-certificates"></a>Mesclar certificados intermediários

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

#### <a name="export-certificate-to-pfx"></a>Exportar o certificado para PFX

Exporte o certificado TLS/SSL mesclado com a chave privada com a qual a solicitação de certificado foi gerada.

Se você gerou a solicitação de certificado usando o OpenSSL, isso significa que você criou um arquivo de chave privada. Para exportar o certificado para PFX, execute o comando a seguir. Substitua os espaços reservados _&lt;private-key-file>_ e _&lt;merged-certificate-file>_ pelos caminhos para a sua chave privada e o arquivo de certificado mesclado.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

Quando solicitado, defina uma senha de exportação. Você usará essa senha ao carregar seu certificado TLS/SSL para o Azure Key Vault posteriormente.

Se você usou o IIS ou o _Certreq.exe_ para gerar a solicitação de certificado, instale o certificado no computador local e, em seguida, [exporte o certificado para PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="save-certificate-in-key-vault"></a>Salvar o certificado no Key Vault
O procedimento para importar um certificado requer que o arquivo codificado como PEM ou PFX esteja no disco e que você tenha a chave privada. 
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Para carregar seu certificado autoassinado no cofre de chaves:
1. Vá para a instância do cofre de chaves.
1. No painel de navegação esquerdo, clique em **Certificados**.
1. No menu superior, clique em **Gerar/importar**.
1. Na caixa de diálogo **Criar um certificado** em **Método de criação de certificado**, selecione `Import`.
1. Em **Carregar o Arquivo de Certificado**, navegue até a localização do certificado e selecione-o.
1. Em **Senha**, insira a chave privada para o certificado.
1. Clique em **Criar**.

    ![Importar certificado 1](./media/custom-dns-tutorial/import-certificate-a.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```azurecli
az keyvault certificate import --file <path to .pfx file> --name <certificate name> --vault-name <key vault name> --password <export password>
```
---

### <a name="grant-azure-spring-cloud-access-to-your-key-vault"></a>Conceder ao Azure Spring Cloud acesso ao seu cofre de chaves

Conceda ao Azure Spring Cloud acesso ao seu cofre de chaves para importar o certificado:
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Vá para a instância do cofre de chaves.
1. No painel de navegação esquerdo, clique em **Política de Acesso**.
1. No menu superior, clique em **Adicionar Política de Acesso**.
1. Preencha as informações e clique no botão **Adicionar** e, em seguida, **Salve** a política de acesso.

| Permissão de segredo | Permissão de certificado | Selecionar entidade de segurança |
|--|--|--|
| Obter, listar | Obter, listar | Gerenciamento de domínio do Azure Spring Cloud |

![Importar certificado 2](./media/custom-dns-tutorial/import-certificate-b.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Conceda ao Azure Spring Cloud acesso de leitura ao cofre de chaves, substitua `<key vault resource group>` e `<key vault name>` no comando a seguir.
```
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list --secret-permissions get list
``` 
---

### <a name="import-certificate-to-azure-spring-cloud"></a>Importar o certificado para o Azure Spring Cloud
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
1. Vá para a instância do serviço. 
1. No painel de navegação à esquerda do aplicativo, selecione **Configurações de TLS/SSL**.
1. Em seguida, clique em **Importar o Certificado do Key Vault**.

    ![Importar certificado](./media/custom-dns-tutorial/import-certificate.png)

1. Depois de importar o certificado com êxito, você o verá na lista de **Certificados de Chave Privada**.

    ![Certificado de chave privada](./media/custom-dns-tutorial/key-certificates.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

Para mostrar uma lista de certificados importados:

```
az spring-cloud certificate list --resource-group <resource group name> --service <service name>
```
---

> [!IMPORTANT] 
> Para proteger um domínio personalizado com esse certificado, ainda é necessário associar o certificado a um domínio específico. Siga as etapas nesta seção: [Adicionar Associação SSL](#add-ssl-binding).

## <a name="add-custom-domain"></a>Adicionar domínio personalizado
Você pode usar um registro CNAME para mapear um nome DNS personalizado para o Azure Spring Cloud. 

> [!NOTE] 
> Não há suporte para o registro A. 

### <a name="create-the-cname-record"></a>Criar um registro CNAME
Acesse seu provedor DNS e adicione um registro CNAME para mapear seu domínio para o <nome_do_serviço>.azuremicroservices.io. Aqui, <nome_do_serviço> é o nome da sua instância do Azure Spring Cloud. Damos suporte a domínio e subdomínio de caractere curinga. Depois de adicionar o CNAME, a página de registros DNS será parecida com o seguinte exemplo: 

![Página de Registros DNS](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>Mapear o domínio personalizado para o aplicativo do Azure Spring Cloud
Se você não tiver um aplicativo no Azure Spring Cloud, siga as instruções em [Início Rápido: Iniciar um aplicativo existente do Azure Spring Cloud usando o portal do Azure](./spring-cloud-quickstart.md).

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Acesse a página do aplicativo.

1. Selecione **Domínio Personalizado**.
2. Em seguida, selecione **Adicionar Domínio Personalizado**. 

    ![Domínio personalizado](./media/custom-dns-tutorial/custom-domain.png)

3. Digite o nome de domínio totalmente qualificado ao qual você adicionou um registro CNAME, como www.contoso.com. Verifique se o tipo de registro de nome de host está definido como CNAME (<nome_do_serviço>.azuremicroservices.io)
4. Clique em **Validar** para habilitar o botão **Adicionar**.
5. Clique em **Adicionar**.

    ![Adicionar domínio personalizado](./media/custom-dns-tutorial/add-custom-domain.png)

Um aplicativo pode ter vários domínios, mas um domínio só pode ser mapeado para um aplicativo. Quando você tiver mapeado com êxito seu domínio personalizado para o aplicativo, você o verá na tabela de domínios personalizados.

![Tabela de domínios personalizados](./media/custom-dns-tutorial/custom-domain-table.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> --resource-group <resource group name> --service <service name>
```

Para mostrar a lista de domínios personalizados:
```
az spring-cloud app custom-domain list --app <app name> --resource-group <resource group name> --service <service name>
```
---

> [!NOTE]
> Um rótulo **Não Seguro** para seu domínio personalizado significa que ele ainda não está associado a um certificado SSL. Solicitações HTTPS de um navegador para seu domínio personalizado receberão um erro ou aviso.

## <a name="add-ssl-binding"></a>Adicionar associação SSL

#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Na tabela de domínio personalizado, selecione **Adicionar associação SSL** conforme mostrado na figura anterior.  
1. Selecione seu **certificado** ou importe-o.
1. Clique em **Save** (Salvar).

    ![Adicionar associação SSL 1](./media/custom-dns-tutorial/add-ssl-binding.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```
---

Depois de adicionar a associação SSL com êxito, o estado do domínio será seguro: **Íntegro**. 

![Adicionar associação SSL 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>Impor HTTPS
Por padrão, qualquer pessoa ainda pode acessar seu aplicativo usando HTTP, mas você pode redirecionar todas as solicitações HTTP para a porta HTTPS.
#### <a name="portal"></a>[Portal](#tab/Azure-portal)
Na sua página do aplicativo, na navegação esquerda, selecione **Domínio Personalizado**. Em seguida, defina **Somente HTTPS** como *Verdadeiro*.

![Adicionar associação SSL 3](./media/custom-dns-tutorial/enforce-http.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```
az spring-cloud app update -n <app name> --resource-group <resource group name> --service <service name> --https-only
```
---
Quando a operação estiver concluída, navegue até qualquer uma das URLs HTTPS que aponte para seu aplicativo. Observe que as URLs HTTP não funcionam.

## <a name="see-also"></a>Confira também
* [O que é o Cofre da Chave do Azure?](../key-vault/general/overview.md)
* [Importar um certificado](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)
* [Iniciar o aplicativo Spring Cloud usando a CLI do Azure](./spring-cloud-quickstart.md)
