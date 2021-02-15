---
title: Configurar o TLS para um serviço de nuvem | Microsoft Docs
description: Saiba como especificar um ponto de extremidade HTTPS para uma função Web e como carregar um certificado TLS/SSL para proteger seu aplicativo. Esses exemplos usam o portal do Azure.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 33aa088efd7768153d4a17472d82e0826f4ffa6b
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742634"
---
# <a name="configuring-tls-for-an-application-in-azure"></a>Configurando o TLS para um aplicativo no Azure

> [!IMPORTANT]
> Os [serviços de nuvem do Azure (suporte estendido)](../cloud-services-extended-support/overview.md) são um novo modelo de implantação baseado em Azure Resource Manager para o produto de serviços de nuvem do Azure.Com essa alteração, os serviços de nuvem do Azure em execução no modelo de implantação baseado no Azure Service Manager foram renomeados como serviços de nuvem (clássicos) e todas as novas implantações devem usar os [serviços de nuvem (suporte estendido)](../cloud-services-extended-support/overview.md).

A TLS (segurança de camada de transporte), anteriormente conhecida como criptografia SSL, é o método mais usado para proteger os dados enviados pela Internet. Esta tarefa comum discute como especificar um ponto de extremidade HTTPS para uma função Web e como carregar um certificado TLS/SSL para proteger seu aplicativo.

> [!NOTE]
> Os procedimentos descritos nesta tarefa se aplicam aos Serviços de Nuvem do Azure; para os Serviços de Aplicativos, veja [isto](../app-service/configure-ssl-bindings.md).
>

Essa tarefa usa uma implantação de produção. Informações sobre o uso de uma implantação de preparo são fornecidas no final deste tópico.

Leia [isto](cloud-services-how-to-create-deploy-portal.md) primeiro se você ainda não tiver criado um serviço de nuvem.

## <a name="step-1-get-a-tlsssl-certificate"></a>Etapa 1: obter um certificado TLS/SSL
Para configurar o TLS para um aplicativo, primeiro você precisa obter um certificado TLS/SSL que tenha sido assinado por uma autoridade de certificação (CA), um terceiro confiável que emite certificados para essa finalidade. Se você ainda não tiver um, precisará obter um de uma empresa que vende certificados TLS/SSL.

O certificado deve atender aos seguintes requisitos para certificados TLS/SSL no Azure:

* O certificado deve conter uma chave pública.
* O certificado deve ser criado para troca de chaves, exportável para um arquivo Troca de Informações Pessoais (.pfx).
* O nome de assunto do certificado deve corresponder ao domínio usado para acessar o serviço de nuvem. Não é possível obter um certificado TLS/SSL de uma autoridade de certificação (CA) para o domínio cloudapp.net. Você deve adquirir um nome de domínio personalizado para usar quando acessar o serviço. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para acessar o aplicativo. Por exemplo, se o nome de domínio personalizado for **contoso.com** , você solicitará um certificado de sua autoridade de certificação para **_. contoso.com_* ou **www \. contoso.com**.
* O certificado deve usar, no mínimo, uma criptografia de 2048 bits.

Para fins de teste, você pode [criar](cloud-services-certs-create.md) e usar um certificado autoassinado. Um certificado autoassinado não é autenticado por meio de uma autoridade de certificação e pode usar o domínio cloudapp.net como a URL do site. Por exemplo, a tarefa a seguir usa um certificado autoassinado na qual o nome comum (CN) utilizado no certificado é **sslexample.cloudapp.net**.

Em seguida, você deve incluir informações sobre o certificado nos arquivos de definição e configuração do serviço.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Etapa 2: Modificar a definição de serviço e arquivos de configuração
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

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Etapa 4: Conectar-se à instância da função usando HTTPS
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
   > Se você quiser usar o TLS para uma implantação de preparo em vez de uma implantação de produção, primeiro será necessário determinar a URL usada para a implantação de preparo. Após o serviço de nuvem ter sido implantado, a URL para o ambiente de preparo será determinada pelo GUID da **ID da Implantação** neste formato: `https://deployment-id.cloudapp.net/`  
   >
   > Criar um certificado com o nome comum (CN) igual à URL baseada em GUID (por exemplo, **328187776e774ceda8fc57609d404462.cloudapp.net**). Use o portal para adicionar o certificado ao serviço de nuvem preparado. Em seguida, adicione as informações de certificado aos arquivos CSDEF e CSCFG, reempacote seu aplicativo e atualize a implantação preparada usando o novo pacote.
   >

## <a name="next-steps"></a>Próximas etapas
* [Configuração geral do serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerenciar seu serviço de nuvem](cloud-services-how-to-manage-portal.md).



