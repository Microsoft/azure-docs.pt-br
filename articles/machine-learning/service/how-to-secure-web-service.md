---
title: Proteger o servidor Web com SSL
titleSuffix: Azure Machine Learning service
description: Saiba como proteger um serviço web implantado com o serviço de Azure Machine Learning, habilitando o HTTPS. HTTPS protege os dados enviados por clientes que usam o protocolo TLS (TLS), uma substituição para camadas de soquete seguro (SSL). Ele também é usado pelos clientes para verificar a identidade do serviço web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 02/05/2019
ms.custom: seodec18
ms.openlocfilehash: 1a6aa75f3d25cd88cd1edb9b2cdcfabc3b4ec8f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818532"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>Usar o SSL para proteger os serviços Web com o Serviço do Azure Machine Learning

Neste artigo, você aprenderá como proteger um serviço Web implantado com o serviço do Azure Machine Learning. Você pode restringir o acesso a serviços da web e proteger os dados enviados por clientes que usam [o protocolo seguro (HTTPS)](https://en.wikipedia.org/wiki/HTTPS).

HTTPS é usado para proteger as comunicações entre um cliente e o serviço web ao criptografar as comunicações entre os dois. Criptografia é tratada usando [segurança de camada de transporte (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Às vezes, isso ainda é conhecido como protocolo (SSL), que foi o predecessor TLS.

> [!TIP]
> O SDK do Azure Machine Learning usa o termo 'SSL' para propriedades relacionado à habilitação de comunicações seguras. Isso não significa que o TLS não é usado pelo seu serviço web, apenas SSL é o termo mais reconhecível para muitos leitores.

TLS e SSL ambos dependem __certificados digitais__, que são usados para executar a verificação de identidade e criptografia. Para obter mais informações sobre certificados digitais como funcionam, consulte a entrada da Wikipedia sobre [infraestrutura de chave pública (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!Warning]
> Se você não habilitar e usar HTTPS para o serviço web, dados enviados para e do serviço podem ser visíveis para outras pessoas na internet.
>
> HTTPS também permite que o cliente verificar a autenticidade do servidor que ele está se conectando. Isso protege os clientes contra [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) ataques.

O processo de proteção de um serviço web novo ou existente é da seguinte maneira:

1. Obter um nome de domínio.

2. Obtenha um certificado digital.

3. Implantar ou atualizar o serviço Web com a configuração de SSL habilitada.

4. Atualizar o DNS para apontar para o serviço Web.

Há pequenas diferenças ao proteger serviços Web entre os [destinos de implantação](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Obter um nome de domínio

Se você ainda não tem um nome de domínio, pode comprá-lo de um __registrador de nome de domínio__. O processo é diferente entre registradores, assim como o custo. O registrador também fornece ferramentas para gerenciar o nome de domínio. Essas ferramentas são usadas para mapear um nome de domínio totalmente qualificado (como www\.contoso.com) para o endereço IP que o serviço web de hospedagem.

## <a name="get-an-ssl-certificate"></a>Obter um certificado SSL

Há várias maneiras de obter um certificado SSL (certificado digital). A mais comum é comprar um de uma __CA__ (autoridade de certificação). Independentemente de onde você obtém o certificado, é necessário ter os seguintes arquivos:

* Um __certificado__. O certificado deve conter a cadeia de certificados completa e deve ser codificado em PEM.
* Uma __chave__. A chave deve ser codificada em PEM.

Ao solicitar um certificado, você precisa fornecer o FQDN (nome de domínio totalmente qualificado) do endereço que planeja usar para o serviço Web. Por exemplo, www\.contoso.com. O endereço identificado no certificado e o endereço usado pelos clientes são comparados na hora de validar a identidade do serviço Web. Se os endereços não coincidem, os clientes recebem um erro.

> [!TIP]
> Se a Autoridade de Certificação não puder fornecer o certificado e a chave como arquivos codificados em PEM, você poderá usar um utilitário como [OpenSSL](https://www.openssl.org/) para alterar o formato.

> [!WARNING]
> Certificados autoassinados devem ser usados apenas para desenvolvimento. Eles não devem ser usados na produção. Certificados autoassinados podem causar problemas nos aplicativos clientes. Para obter mais informações, consulte a documentação das bibliotecas de rede usadas no aplicativo cliente.

## <a name="enable-ssl-and-deploy"></a>Habilitar SSL e implantar

Para implantar (ou reimplantar) o serviço com SSL habilitado, defina o parâmetro `ssl_enabled` para `True`, conforme o necessário. Defina o parâmetro `ssl_certificate` como o valor do arquivo __certificate__ e a `ssl_key` como o valor do arquivo __key__.

+ **Implantar no AKS (Serviço de Kubernetes do Azure)**

  Ao provisionar o cluster do AKS, forneça valores para parâmetros relacionados a SSL, conforme é mostrado no snippet de código:

    ```python
    from azureml.core.compute import AksCompute

    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Implantar nas ACI (Instâncias de Contêiner do Azure)**

  Durante a implantação nas ACI, forneça valores para os parâmetros relacionados a SSL, conforme é mostrado no snippet de código:

    ```python
    from azureml.core.webservice import AciWebservice

    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

+ **Implantar em FPGA (Matrizes de Portas Programáveis em Campo)**

  Durante a implantação em FPGA, forneça valores para os parâmetros relacionados a SSL, conforme é mostrado no trecho de código:

    ```python
    from azureml.contrib.brainwave import BrainwaveWebservice

    deployment_config = BrainwaveWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem")
    ```

## <a name="update-your-dns"></a>Atualizar o DNS

Em seguida, você precisa atualizar o DNS para apontar para o serviço Web.

+ **Para ACI**:

  Use as ferramentas fornecidas pelo registrador de nome de domínio para atualizar o registro DNS do nome de domínio. O registro deve apontar para o endereço IP do serviço.

  Dependendo do registrador e da TTL (vida útil) configurados para o nome de domínio, pode levar vários minutos a várias horas até que os clientes possam resolver o nome de domínio.

+ **Para AKS**:

  Atualize o DNS na guia "Configuração" do "Endereço IP Público" do cluster do AKS, conforme é mostrado na imagem. Encontre o endereço IP público como um dos tipos de recursos criados no grupo de recursos que contém os nós de agente do AKS e outros recursos de rede.

  ![Serviço do Azure Machine Learning: Proteger os serviços Web com SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Próximas etapas
Saiba como:
+ [Consumir um modelo de machine learning implantado como um serviço web](how-to-consume-web-service.md)
+ [Executar experimentos e fazer inferências com segurança dentro de uma Rede Virtual do Azure](how-to-enable-virtual-network.md)
