---
title: Proteger serviços Web usando TLS
titleSuffix: Azure Machine Learning
description: Saiba como habilitar o HTTPS com a versão 1,2 do TLS para proteger um serviço Web que é implantado por meio de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.date: 01/04/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 68163f7ca8cc1b37bda4e1224330f966265554c2
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99980465"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Use o TLS para proteger um serviço Web por meio do Azure Machine Learning


Este artigo mostra como proteger um serviço Web que é implantado por meio de Azure Machine Learning.

Você usa [https](https://en.wikipedia.org/wiki/HTTPS) para restringir o acesso aos serviços da Web e proteger os dados que os clientes enviam. O HTTPS ajuda a proteger as comunicações entre um cliente e um serviço Web criptografando as comunicações entre os dois. A criptografia usa [TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Às vezes, o TLS ainda é referido como *protocolo SSL* (SSL), que era o PREDECESSOR do TLS.

> [!TIP]
> O SDK do Azure Machine Learning usa o termo "SSL" para propriedades relacionadas a comunicações seguras. Isso não significa que o serviço Web não usa *TLS*. SSL é apenas um termo mais reconhecido.
>
> Especificamente, os serviços Web implantados por meio do Azure Machine Learning suportam o TLS versão 1,2 para AKS e ACI. Para implantações de ACI, se você estiver na versão mais antiga do TLS, recomendamos reimplantar para obter a versão mais recente do TLS.
>
> O TLS versão 1,3 para a inferência de Azure Machine Learning AKS não tem suporte.

O TLS e o SSL dependem de *certificados digitais*, que ajudam com a criptografia e a verificação de identidade. Para obter mais informações sobre como os certificados digitais funcionam, consulte o tópico da Wikipédia [infraestrutura de chave pública](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Se você não usar HTTPS para o serviço Web, os dados enviados de e para o serviço poderão estar visíveis para outras pessoas na Internet.
>
> O HTTPS também permite que o cliente Verifique a autenticidade do servidor ao qual ele está se conectando. Esse recurso protege os clientes contra ataques [Man-in-the-Middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) .

Esse é o processo geral para proteger um serviço Web:

1. Obter um nome de domínio.

2. Obter um certificado digital.

3. Implante ou atualize o serviço Web com TLS habilitado.

4. Atualizar o DNS para apontar para o serviço Web.

> [!IMPORTANT]
> Se estiver implantando no AKS (serviço kubernetes do Azure), você poderá comprar seu próprio certificado ou usar um certificado fornecido pela Microsoft. Se você usar um certificado da Microsoft, não precisará obter um nome de domínio ou um certificado TLS/SSL. Para obter mais informações, consulte a seção [habilitar TLS e implantar](#enable) deste artigo.

Há pequenas diferenças quando você protege os s entre os [destinos de implantação](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Obter um nome de domínio

Se você ainda não possui um nome de domínio, compre um de um *registrador de nome de domínio*. O processo e o preço são diferentes entre os registradores. O registrador fornece ferramentas para gerenciar o nome de domínio. Você usa essas ferramentas para mapear um FQDN (nome de domínio totalmente qualificado) (como www \. contoso.com) para o endereço IP que hospeda o serviço Web.

## <a name="get-a-tlsssl-certificate"></a>Obter um certificado TLS/SSL

Há várias maneiras de obter um certificado TLS/SSL (certificado digital). O mais comum é comprar um de uma *autoridade de certificação* (CA). Independentemente de onde você obtém o certificado, você precisa dos seguintes arquivos:

* Um **certificado**. O certificado deve conter a cadeia de certificados completa e deve ser "codificado por PEM".
* Uma **chave**. A chave também deve ser codificada por PEM.

Ao solicitar um certificado, você deve fornecer o FQDN do endereço que planeja usar para o serviço Web (por exemplo, www \. contoso.com). O endereço que é carimbado no certificado e o endereço que os clientes usam são comparados para verificar a identidade do serviço Web. Se esses endereços não corresponderem, o cliente receberá uma mensagem de erro.

> [!TIP]
> Se a autoridade de certificação não puder fornecer o certificado e a chave como arquivos codificados por PEM, você poderá usar um utilitário como o [OpenSSL](https://www.openssl.org/) para alterar o formato.

> [!WARNING]
> Use certificados *autoassinados* somente para desenvolvimento. Não os use em ambientes de produção. Certificados autoassinados podem causar problemas nos aplicativos clientes. Para obter mais informações, consulte a documentação das bibliotecas de rede que seu aplicativo cliente usa.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> Habilitar TLS e implantar

**Para a implantação do AKS**, você pode habilitar o encerramento de TLS ao [criar ou anexar um cluster AKs](how-to-create-attach-kubernetes.md) no espaço de trabalho AML. No momento da implantação do modelo AKS, você pode desabilitar a terminação de TLS com o objeto de configuração de implantação; caso contrário, toda a implantação do modelo AKS terá a terminação TLS habilitada em AKS criar ou anexar tempo.

Para a implantação do ACI, você pode habilitar a terminação de TLS no momento da implantação do modelo com o objeto de configuração de implantação.


### <a name="deploy-on-azure-kubernetes-service"></a>Implantar no serviço kubernetes do Azure

  > [!NOTE]
  > As informações nesta seção também se aplicam quando você implanta um serviço Web seguro para o designer. Se você não estiver familiarizado com o uso do SDK do Python, consulte [o que é o SDK do Azure Machine Learning para Python?](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

Ao [criar ou anexar um cluster AKs](how-to-create-attach-kubernetes.md) no espaço de trabalho AML, você pode habilitar a terminação de TLS com os objetos de configuração **[AksCompute.provisioning_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueprovisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** e **[AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueattach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . O método retorna um objeto de configuração que tem um método **Enable_ssl** , e você pode usar **Enable_ssl** método para habilitar o TLS.

Você pode habilitar o TLS com o certificado da Microsoft ou com um certificado personalizado adquirido da AC. 

* **Ao usar um certificado da Microsoft**, você deve usar o parâmetro *leaf_domain_label* . Esse parâmetro gera o nome DNS para o serviço. Por exemplo, um valor "contoso" cria um nome de domínio "Contoso \<six-random-characters> . \<azureregion> . cloudapp.azure.com ", em que \<azureregion> é a região que contém o serviço. Opcionalmente, você pode usar o parâmetro *overwrite_existing_domain* para substituir o *leaf_domain_label* existente. O exemplo a seguir demonstra como criar uma configuração que habilita um TLS com o certificado da Microsoft:

    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```
    > [!IMPORTANT]
    > Ao usar um certificado da Microsoft, você não precisa comprar seu próprio certificado ou nome de domínio.

    > [!WARNING]
    > Se o cluster AKS estiver configurado com um balanceador de carga interno, __não haverá suporte__ para o uso de um certificado fornecido pela Microsoft e você deverá usar o certificado personalizado para habilitar o TLS.

* **Ao usar um certificado personalizado que você comprou**, use os parâmetros *ssl_cert_pem_file*, *ssl_key_pem_file* e *ssl_cname* . O exemplo a seguir demonstra como usar arquivos. pem para criar uma configuração que usa um certificado TLS/SSL que você comprou:
 
    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")

    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Para obter mais informações sobre *Enable_ssl*, consulte [AksProvisioningConfiguration.Enable_ssl ()](/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) e [AksAttachConfiguration.Enable_ssl ()](/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Implantar nas Instâncias de Contêiner do Azure

Ao implantar em instâncias de contêiner do Azure, você fornece valores para parâmetros relacionados a TLS, como mostra o trecho de código a seguir:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Para obter mais informações, consulte [AciWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Atualizar o DNS

Para a implantação do AKS com certificado personalizado ou implantação do ACI, você deve atualizar o registro DNS para apontar para o endereço IP do ponto de extremidade de pontuação.

  > [!IMPORTANT]
  > Ao usar um certificado da Microsoft para a implantação do AKS, você não precisa atualizar manualmente o valor DNS para o cluster. O valor deve ser definido automaticamente.

Você pode seguir as etapas a seguir para atualizar o registro DNS para seu nome de domínio personalizado:
* Obtenha o endereço IP do ponto de extremidade de Pontuação do URI do ponto de extremidade de pontuação, que geralmente está no formato de *http://104.214.29.152:80/api/v1/service/<service-name>/score* . 
* Use as ferramentas do seu registrador de nome de domínio para atualizar o registro DNS para seu nome de domínio. O registro deve apontar para o endereço IP do ponto de extremidade de pontuação.
* Após a atualização do registro DNS, você pode validar a resolução DNS usando o comando *nslookup Custom-Domain-Name* . Se o registro DNS for atualizado corretamente, o nome de domínio personalizado apontará para o endereço IP do ponto de extremidade de pontuação.
* Pode haver um atraso de minutos ou horas antes que os clientes possam resolver o nome de domínio, dependendo do registrador e a TTL (vida útil) configurada para o nome de domínio.


## <a name="update-the-tlsssl-certificate"></a>Atualizar o certificado TLS/SSL

Os certificados TLS/SSL expiram e devem ser renovados. Normalmente, isso acontece todos os anos. Use as informações nas seções a seguir para atualizar e renovar seu certificado para modelos implantados no serviço kubernetes do Azure:

### <a name="update-a-microsoft-generated-certificate"></a>Atualizar um certificado gerado pela Microsoft

Se o certificado foi originalmente gerado pela Microsoft (ao usar o *leaf_domain_label* para criar o serviço), **ele será renovado automaticamente** quando necessário. Se você quiser renová-lo manualmente, use um dos exemplos a seguir para atualizar o certificado:

> [!IMPORTANT]
> * Se o certificado existente ainda for válido, use `renew=True` (SDK) ou `--ssl-renew` (CLI) para forçar a configuração a renová-la. Por exemplo, se o certificado existente ainda for válido por 10 dias e você não usar `renew=True` , o certificado poderá não ser renovado.
> * Quando o serviço foi implantado originalmente, o `leaf_domain_label` é usado para criar um nome DNS usando o padrão `<leaf-domain-label>######.<azure-region>.cloudapp.azure.net` . Para preservar o nome existente (incluindo os 6 dígitos originalmente gerados), use o `leaf_domain_label` valor original. Não inclua os 6 dígitos que foram gerados.

**Usar o SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Usar a CLI**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

Para obter mais informações, consulte os documentos de referência a seguir:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?preserve-view=true&view=azure-ml-py)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?preserve-view=true&view=azure-ml-py)

### <a name="update-custom-certificate"></a>Atualizar certificado personalizado

Se o certificado foi originalmente gerado por uma autoridade de certificação, use as seguintes etapas:

1. Use a documentação fornecida pela autoridade de certificação para renovar o certificado. Esse processo cria novos arquivos de certificado.

1. Use o SDK ou a CLI para atualizar o serviço com o novo certificado:

    **Usar o SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **Usar a CLI**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Para obter mais informações, consulte os documentos de referência a seguir:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?preserve-view=true&view=azure-ml-py)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?preserve-view=true&view=azure-ml-py)

## <a name="disable-tls"></a>Desabilitar TLS

Para desabilitar o TLS para um modelo implantado no serviço kubernetes do Azure, crie um `SslConfiguration` com `status="Disabled"` e, em seguida, execute uma atualização:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Próximas etapas
Saiba como:
+ [Consumir um modelo de aprendizado de máquina implantado como um serviço Web](how-to-consume-web-service.md)
+ [Visão geral de isolamento de rede virtual e privacidade](how-to-network-security-overview.md)