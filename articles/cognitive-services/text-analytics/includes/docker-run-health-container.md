---
title: Exemplo de execução de contêiner do comando de execução do Docker
titleSuffix: Azure Cognitive Services
description: Comando de execução do Docker para o contêiner de integridade
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/12/2020
ms.author: aahi
ms.openlocfilehash: af8fec56c32b52e2af584e59f08db6cc7129c9c5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947356"
---
## <a name="install-the-container"></a>Instalar o contêiner

Há várias maneiras que você pode instalar e executar o Análise de Texto para o contêiner de integridade. 

- Use o [portal do Azure](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare) para criar um recurso de análise de texto e use o Docker para obter seu contêiner.
- Use os scripts do PowerShell e do CLI do Azure a seguir para automatizar a implantação de recursos e a configuração do contêiner.

### <a name="run-the-container-locally"></a>Executar o contêiner localmente

Para executar o contêiner em seu próprio ambiente depois de baixar a imagem de contêiner, localize sua ID de imagem:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Execute o comando a seguir `docker run` . Substitua os espaços reservados abaixo pelos seus próprios valores:

| Espaço reservado | Valor | Formato ou exemplo |
|-------------|-------|---|
| **{API_KEY}** | A chave para o recurso de Análise de Texto. Você pode encontrá-lo na página de **ponto de extremidade e chave** do recurso, na portal do Azure. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | O ponto de extremidade para acessar o API de Análise de Texto. Você pode encontrá-lo na página de **ponto de extremidade e chave** do recurso, na portal do Azure. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | A ID da imagem do seu contêiner. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | O diretório de entrada para o contêiner. | Windows: `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

Esse comando:

- Supõe que o diretório de entrada exista no computador host
- Executa um Análise de Texto para o contêiner de integridade da imagem de contêiner
- Aloca 6 núcleos de CPU e 12 gigabytes (GB) de memória
- Expõe a porta TCP 5000 e aloca um pseudo-TTY para o contêiner
- Remove automaticamente o contêiner depois que ele sai. A imagem de contêiner ainda fica disponível no computador host.

### <a name="demo-ui-to-visualize-output"></a>Interface do usuário de demonstração para visualizar a saída

> [!NOTE]
> A demonstração só está disponível com o Análise de Texto para o contêiner de integridade.

O contêiner fornece APIs de ponto de extremidade de previsão de consulta baseadas em REST.  Também fornecemos uma ferramenta de visualização no contêiner que é acessível acrescentando `/demo` ao ponto de extremidade do contêiner. Por exemplo:

```
http://<serverURL>:5000/demo
```

Use a solicitação de ondulação de exemplo abaixo para enviar uma consulta ao contêiner que você implantou, substituindo a `serverURL` variável pelo valor apropriado.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Instalar o contêiner usando o Azure Aplicativo Web para Contêineres

O Azure [aplicativo Web para contêineres](https://azure.microsoft.com/services/app-service/containers/) é um recurso do Azure dedicado à execução de contêineres na nuvem. Ele traz recursos prontos para uso, como dimensionamento automático, suporte a contêineres do Docker e ao Docker Compose, suporte a HTTPS e muito mais.

> [!NOTE]
> Usando o aplicativo Web do Azure, você obterá automaticamente um domínio na forma de `<appservice_name>.azurewebsites.net`

Execute este script do PowerShell usando o CLI do Azure para criar um Aplicativo Web para Contêineres, usando sua assinatura e a imagem de contêiner sobre HTTPS. Aguarde até que o script seja concluído (aproximadamente 25-30 minutos) antes de enviar a primeira solicitação.

```azurecli
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Instalar o contêiner usando a instância de contêiner do Azure

Você também pode usar uma ACI (instância de contêiner do Azure) para facilitar a implantação. ACI é um recurso que permite executar contêineres do Docker sob demanda em um ambiente gerenciado e sem servidor do Azure. 

Consulte [como usar as instâncias de contêiner do Azure](../../containers/azure-container-instance-recipe.md) para obter etapas sobre como implantar um recurso ACI usando o portal do Azure. Você também pode usar o script do PowerShell abaixo usando CLI do Azure, que criará um ACI em sua assinatura usando a imagem de contêiner.  Aguarde até que o script seja concluído (aproximadamente 25-30 minutos) antes de enviar a primeira solicitação.  Devido ao limite do número máximo de CPUs por recurso ACI, não selecione esta opção se você espera enviar mais de 5 documentos grandes (aproximadamente 5000 caracteres cada) por solicitação.
Consulte o artigo de [suporte regional do ACI](../../../container-instances/container-instances-region-availability.md) para obter informações de disponibilidade. 

> [!NOTE] 
> As instâncias de contêiner do Azure não incluem suporte a HTTPS para os domínios internos. Se você precisar de HTTPS, será necessário configurá-lo manualmente, incluindo a criação de um certificado e o registro de um domínio. Você pode encontrar instruções para fazer isso com o NGINX abaixo.

```azurecli
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Conectividade segura do ACI

Por padrão, não há nenhuma segurança fornecida ao usar ACI com a API do contêiner. Isso ocorre porque normalmente os contêineres serão executados como parte de um pod que é protegido do exterior por uma ponte de rede. No entanto, você pode modificar um contêiner com um componente front-end, mantendo o ponto de extremidade do contêiner privado. Os exemplos a seguir usam [Nginx](https://www.nginx.com) como um gateway de entrada para dar suporte a autenticação HTTPS/SSL e certificado de cliente.

> [!NOTE]
> NGINX é um servidor e proxy HTTP de software livre e de alto desempenho. Um contêiner NGINX pode ser usado para encerrar uma conexão TLS para um único contêiner. Também são possíveis soluções de terminação TLS baseadas em NGINX de entrada mais complexas.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Configurar o NGINX como um gateway de entrada

O NGINX usa [arquivos de configuração](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) para habilitar recursos em tempo de execução. Para habilitar o encerramento de TLS para outro serviço, você deve especificar um certificado SSL para encerrar a conexão TLS e  `proxy_pass` especificar um endereço para o serviço. Um exemplo é fornecido abaixo.


> [!NOTE]
> `ssl_certificate` Espera que um caminho seja especificado no sistema de arquivos local do contêiner NGINX. O endereço especificado para `proxy_pass` deve estar disponível na rede do contêiner nginx.

O contêiner NGINX carregará todos os arquivos no `_.conf_` que estão montados em no `/etc/nginx/conf.d/` caminho de configuração de http.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Exemplo de arquivo Docker Compose

O exemplo abaixo mostra como um arquivo [Docker Compose](https://docs.docker.com/compose/reference/overview) pode ser criado para implantar o Nginx e o análise de texto para os contêineres de integridade:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Para iniciar esse arquivo do Docker Compose, execute o seguinte comando em um console no nível raiz do arquivo:

```bash
docker-compose up
```

Para obter mais informações, consulte a documentação do NGINX na [terminação do SSL do Nginx](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).