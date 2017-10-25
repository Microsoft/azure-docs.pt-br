---
title: "Configurar o SSL para um serviço de nuvem (clássico) | Microsoft Docs"
description: "Saiba como especificar um ponto de extremidade HTTPS para uma função Web e como carregar um certificado SSL para proteger seu aplicativo."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 4cbb7f38-7994-454d-b4f0-7259b558c766
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/14/2016
ms.author: adegeo
ms.openlocfilehash: 7df2371f64f0d8a9fabc0df37c17d4dfbc47cd7e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>Configurando SSL para um aplicativo no Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](cloud-services-configure-ssl-certificate-portal.md)
> * [Portal clássico do Azure](cloud-services-configure-ssl-certificate.md)
> 
> 

A criptografia SSL (Secure Socket Layer) é o método mais usado para proteger dados enviados pela Internet. Esta tarefa comum aborda como especificar um ponto de extremidade HTTPS para uma função Web e como carregar um certificado SSL para proteger seu aplicativo.

> [!NOTE]
> Os procedimentos descritos nesta tarefa se aplicam aos Serviços de Nuvem do Azure; para os Serviços de Aplicativos, veja [este](../app-service/app-service-web-tutorial-custom-ssl.md) artigo.
> 
> 

Essa tarefa usa uma implantação de produção. Informações sobre o uso de uma implantação de preparo são fornecidas no final deste tópico.

Leia [este](cloud-services-how-to-create-deploy.md) artigo primeiro se você ainda não tiver criado um serviço de nuvem.

## <a name="step-1-get-an-ssl-certificate"></a>Etapa 1: Obter um certificado SSL
Para configurar SSL de um aplicativo, você deve primeiro obter um certificado SSL assinado por uma Autoridade de Certificação (CA), um terceiro confiável que emita certificados com essa finalidade. Se ainda não tiver um, você precisará obtê-lo junto a uma empresa que venda certificados SSL.

O certificado deve atender aos seguintes requisitos para certificados SSL no Azure:

* O certificado deve conter uma chave privada.
* O certificado deve ser criado para troca de chaves, exportável para um arquivo Troca de Informações Pessoais (.pfx).
* O nome de assunto do certificado deve corresponder ao domínio usado para acessar o serviço de nuvem. Você não pode obter um certificado SSL de uma autoridade de certificação (CA) para o domínio cloudapp.net. Você deve adquirir um nome de domínio personalizado para usar quando acessar o serviço. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para acessar o aplicativo. Por exemplo, se o nome de domínio personalizado for **contoso.com**, você pode solicitar um certificado da autoridade de certificação para ***.contoso.com** ou **www.contoso.com**.
* O certificado deve usar, no mínimo, uma criptografia de 2048 bits.

Para fins de teste, você pode [criar](cloud-services-certs-create.md) e usar um certificado autoassinado. Um certificado autoassinado não é autenticado por meio de uma autoridade de certificação e pode usar o domínio cloudapp.net como a URL do site. Por exemplo, a tarefa a seguir usa um certificado autoassinado na qual o nome comum (CN) utilizado no certificado é **sslexample.cloudapp.net**.

Em seguida, você deve incluir informações sobre o certificado nos arquivos de definição e configuração do serviço.

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
   
   As permissões (atributo `permisionLevel`) podem ser definidas como um dos seguintes valores:
   
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

3. No arquivo de definição de serviço, adicione um elemento de **Associação** dentro da seção **Sites**. Essa seção adiciona uma associação HTTP para mapear o ponto de extremidade para o site:
   
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
4. No arquivo de configuração de serviço (CSCFG), ServiceConfiguration.Cloud.cscfg, adicione uma seção **Certificados** na seção **Função**, substituindo o valor de exemplo de impressão digital mostrado abaixo pelo seu certificado:
   
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

(O exemplo acima usa **sha1** para o algoritmo de impressão digital. Especifique o valor apropriado ao algoritmo thumbprint do certificado.)

Agora que os arquivos de definição e configuração do serviço foram atualizados, empacote a implantação para carregamento no Azure. Caso esteja usando **cspack**, não use o sinalizador **/generateConfigurationFile**, pois ele substituirá as informações de certificado que você acabou de inserir.

## <a name="step-3-upload-a-certificate"></a>Etapa 3: Carregar um certificado
O pacote de implantação foi atualizado para usar o certificado, e um ponto de extremidade HTTPS foi adicionado. Agora é possível carregar o pacote e o certificado no Azure com o portal clássico do Azure.

1. Faça logon no [Portal Clássico do Azure][Azure classic portal]. 
2. Clique em **Serviços de Nuvem** no painel de navegação à esquerda.
3. Clique no serviço de nuvem desejado.
4. Clique na guia **Certificados**.
   
    ![Clique na guia Certificados](./media/cloud-services-configure-ssl-certificate/click-cert.png)

5. Clique no botão **Carregar** .
   
    ![Carregar](./media/cloud-services-configure-ssl-certificate/upload-button.png)
    
6. Forneça o **Arquivo**, a **Senha** e depois clique em **Concluir** (a marca de seleção).

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Etapa 4: Conectar-se à instância da função usando HTTPS
Agora que sua implantação está ativa e em execução no Azure, você pode se conectar a ela usando HTTPS.

1. No portal clássico do Azure, selecione a implantação e clique no link em **URL do Site**.
   
   ![Determinar URL do site][2]
2. No navegador da Web, modifique o link para usar **http** sem vez de **http**, e visite a página.
   
   > [!NOTE]
   > Se estiver usando um certificado autoassinado, ao navegar até um ponto de extremidade HTTPS associado com o certificado autoassinado, você poderá ver um erro de certificado no navegador. O uso de um certificado assinado por uma certificação confiável elimina esse problema; enquanto isso, você pode ignorar o erro. (Outra opção é adicionar o certificado autoassinado ao repositório de certificado da autoridade de certificado confiável do usuário.)
   > 
   > 
   
   ![Site de exemplo SSL][3]

Se quiser usar SSL em uma implantação de preparação em lugar de uma implantação da produção, você precisará determinar primeiro o URL usado na implantação de preparação. Implante o serviço de nuvem no ambiente de preparação sem incluir um certificado ou qualquer informação de certificado. Depois de implantado, você poderá determinar a URL baseada em GUID, listada no campo **URL do Site** do portal clássico do Azure. Crie um certificado com o nome comum (CN) igual à URL baseada em GUID (por exemplo, **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**). Use o portal clássico do Azure para adicionar o certificado ao serviço de nuvem preparado. Em seguida, adicione as informações de certificado aos arquivos CSDEF e CSCFG, reempacote seu aplicativo e atualize a implantação preparada usando o novo pacote.

## <a name="next-steps"></a>Próximas etapas
* [Configuração geral do serviço de nuvem](cloud-services-how-to-configure.md).
* Saiba como [implantar um serviço de nuvem](cloud-services-how-to-create-deploy.md).
* Configurar um [nome de domínio personalizado](cloud-services-custom-domain-name.md).
* [Gerenciar seu serviço de nuvem](cloud-services-how-to-manage.md).

[Azure classic portal]: http://manage.windowsazure.com
[0]: ./media/cloud-services-configure-ssl-certificate/CreateCloudService.png
[1]: ./media/cloud-services-configure-ssl-certificate/AddCertificate.png
[2]: ./media/cloud-services-configure-ssl-certificate/CopyURL.png
[3]: ./media/cloud-services-configure-ssl-certificate/SSLCloudService.png
[4]: ./media/cloud-services-configure-ssl-certificate/AddCertificateComplete.png  
