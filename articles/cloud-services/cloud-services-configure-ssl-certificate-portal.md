---
title: Configurar SSL para um serviço de nuvem | Microsoft Docs
description: Saiba como especificar um ponto de extremidade HTTPS para uma função Web e como carregar um certificado SSL para proteger seu aplicativo. Esses exemplos usam o portal do Azure.
services: cloud-services
documentationcenter: .net
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 05/26/2017
ms.author: gwallace
ms.openlocfilehash: 9e7b7526f13fa6b9ae648c4ddb4004a627d85154
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359737"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Configurando SSL para um aplicativo no Azure

A criptografia SSL (Secure Socket Layer) é o método mais usado para proteger dados enviados pela Internet. Esta tarefa comum aborda como especificar um ponto de extremidade HTTPS para uma função Web e como carregar um certificado SSL para proteger seu aplicativo.

> [!NOTE]
> Os procedimentos descritos nesta tarefa se aplicam aos Serviços de Nuvem do Azure; para os Serviços de Aplicativos, veja [isto](../app-service/app-service-web-tutorial-custom-ssl.md).
>

Essa tarefa usa uma implantação de produção. Informações sobre o uso de uma implantação de preparo são fornecidas no final deste tópico.

Leia [isto](cloud-services-how-to-create-deploy-portal.md) primeiro se você ainda não tiver criado um serviço de nuvem.

## <a name="step-1-get-an-ssl-certificate"></a>Etapa 1: Obter um certificado SSL
Para configurar SSL de um aplicativo, você deve primeiro obter um certificado SSL assinado por uma Autoridade de Certificação (CA), um terceiro confiável que emita certificados com essa finalidade. Se ainda não tiver um, você precisará obtê-lo junto a uma empresa que venda certificados SSL.

O certificado deve atender aos seguintes requisitos para certificados SSL no Azure:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, exportável para um arquivo Troca de Informações Pessoais (.pfx).
* O nome de assunto do certificado deve corresponder ao domínio usado para acessar o serviço de nuvem. Você não pode obter um certificado SSL de uma autoridade de certificação (CA) para o domínio cloudapp.net. Você deve adquirir um nome de domínio personalizado para usar quando acessar o serviço. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para acessar o aplicativo. Por exemplo, se o nome de domínio personalizado for **contoso.com** , você solicitará um certificado de sua autoridade de certificação para * **. contoso.com** ou **www\.contoso.com**.
* O certificado deve usar, no mínimo, uma criptografia de 2048 bits.

Para fins de teste, você pode [criar](cloud-services-certs-create.md) e usar um certificado autoassinado. Um certificado autoassinado não é autenticado por meio de uma autoridade de certificação e pode usar o domínio cloudapp.net como a URL do site. Por exemplo, a tarefa a seguir usa um certificado autoassinado na qual o nome comum (CN) utilizado no certificado é **sslexample.cloudapp.net**.

Em seguida, você deve incluir informações sobre o certificado nos arquivos de definição e configuração do serviço.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Etapa 2: Modificar os arquivos de configuração e definição de serviço
O aplicativo deve ser configurado para usar o certificado, e um ponto de extremidade HTTPS deve ser adicionado. Dessa forma, os arquivos de definição e configuração do serviço precisam ser atualizados.

1. No ambiente de desenvolvimento, abra o arquivo de definição de serviço (CSDEF), adicione uma seção **Certificados** dentro da seção **WebRole** e inclua as seguintes informações sobre o certificado (e os certificados intermediários):

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   A seção **Certificados** define o nome do nosso certificado, seu local e o nome do repositório no qual está localizado.

   As permissões (atributo `permissionLevel`) podem ser definidas como um dos seguintes valores:

   | Valor da permissão | Descrição |
   | --- | --- |
   | limitedOrElevated |**(Padrão)** Todos os processos de função podem acessar a chave privada. |
   | elevado |Somente processos elevados podem acessar a chave privada. |

2. No arquivo de definição de serviço, adicione um elemento **InputEndpoint** dentro da seção **Pontos de extremidade** para habilitar HTTPS:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. No arquivo de definição de serviço, adicione um elemento de **Associação** dentro da seção **Sites**. Este elemento adiciona uma associação HTTPS para mapear o ponto de extremidade para o site:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   Todas as alterações obrigatórias no arquivo de definição de serviço foram concluídas, mas você continua precisando adicionar as informações de certificado ao arquivo de configuração de serviço.
4. Em seu arquivo de configuração de serviço (CSCFG), ServiceConfiguration.Cloud.cscfg, adicione um valor de **Certificados** com o do seu certificado. O exemplo de código a seguir fornece detalhes da seção **Certificados**, exceto para o valor de impressão digital.

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(Este exemplo usa **sha1** para o algoritmo de impressão digital. Especifique o valor apropriado ao algoritmo thumbprint do certificado.)

Agora que os arquivos de definição e configuração do serviço foram atualizados, empacote a implantação para carregamento no Azure. Se você está usando **cspack**, não use o sinalizador **/generateConfigurationFile**, pois ele substituirá as informações de certificado que você acabou de inserir.

## <a name="step-3-upload-a-certificate"></a>Etapa 3: Carregar um certificado
Conecte-se ao Portal do Azure e...

1. Na seção **Todos os recursos** do Portal, selecione seu serviço de nuvem.

    ![Publicar o serviço de nuvem](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Clique em **Certificados**.

    ![Clique no ícone de certificados](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Clique em **Carregar** na parte superior da área de certificados.

    ![Clique no item de menu Carregar](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Forneça o **Arquivo**, **Senha** e clique em **Carregar** na parte inferior da área de entrada de dados.

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Etapa 4: Conectar-se à instância de função usando HTTPS
Agora que sua implantação está ativa e em execução no Azure, você pode se conectar a ela usando HTTPS.

1. Clique na **URL do Site** para abrir o navegador da Web.

   ![Clique na URL do site](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. No navegador da Web, modifique o link para usar **http** sem vez de **http**, e visite a página.

   > [!NOTE]
   > Se estiver usando um certificado autoassinado, ao navegar até um ponto de extremidade HTTPS associado com o certificado autoassinado, você poderá ver um erro de certificado no navegador. O uso de um certificado assinado por uma certificação confiável elimina esse problema; enquanto isso, você pode ignorar o erro. (Outra opção é adicionar o certificado autoassinado ao repositório de certificado da autoridade de certificado confiável do usuário.)
   >
   >

   ![Visualização de site](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Se quiser usar SSL em uma implantação de preparação em lugar de uma implantação da produção, você precisará determinar primeiro o URL usado na implantação de preparação. Após o serviço de nuvem ter sido implantado, a URL para o ambiente de preparo será determinada pelo GUID da **ID da Implantação** neste formato: `https://deployment-id.cloudapp.net/`  
   >
   > Criar um certificado com o nome comum (CN) igual à URL baseada em GUID (por exemplo, **328187776e774ceda8fc57609d404462.cloudapp.net**). Use o portal para adicionar o certificado ao serviço de nuvem preparado. Em seguida, adicione as informações de certificado aos arquivos CSDEF e CSCFG, reempacote seu aplicativo e atualize a implantação preparada usando o novo pacote.
   >

## <a name="next-steps"></a>Próximas etapas
* [Configuração geral do serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerenciar seu serviço de nuvem](cloud-services-how-to-manage-portal.md).
