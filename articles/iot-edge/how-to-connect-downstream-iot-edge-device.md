---
title: Conectar dispositivos de IoT Edge downstream – Azure IoT Edge | Microsoft Docs
description: Como configurar um dispositivo de IoT Edge para se conectar a dispositivos de gateway de Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 382cdf87016044748685e5e64ff04ebac53f018d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199130"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway-preview"></a>Conectar um dispositivo de IoT Edge downstream a um gateway de Azure IoT Edge (versão prévia)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Este artigo fornece instruções para estabelecer uma conexão confiável entre um gateway de IoT Edge e um dispositivo de IoT Edge downstream.

>[!NOTE]
>Este recurso exige o IoT Edge versão 1.2, que está em versão prévia pública, que execute contêineres do Linux.
>
>Este artigo reflete a versão de visualização mais recente do IoT Edge versão 1,2. Verifique se o dispositivo está executando a versão [1.2.0-RC4](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc4) ou mais recente. Para obter as etapas para obter a versão de visualização mais recente em seu dispositivo, consulte [instalar o Azure IOT Edge para Linux (versão 1,2)](how-to-install-iot-edge.md) ou [Atualizar IOT Edge para a versão 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

Em um cenário de gateway, um dispositivo IoT Edge pode ser um gateway e um dispositivo downstream. Vários gateways IoT Edge podem ser dispostos em camadas para criar uma hierarquia de dispositivos. Os dispositivos downstream (ou filhos) podem autenticar e enviar ou receber mensagens por meio de seu dispositivo de gateway (ou pai).

Há duas configurações diferentes para IoT Edge dispositivos em uma hierarquia de gateway e este artigo aborda ambos. O primeiro é o dispositivo de IoT Edge de **camada superior** . Quando vários dispositivos IoT Edge estão se conectando entre si, qualquer dispositivo que não tenha um dispositivo pai, mas que se conecta diretamente ao Hub IoT é considerado na camada superior. Este dispositivo é responsável por lidar com solicitações de todos os dispositivos abaixo dela. A outra configuração se aplica a qualquer dispositivo IoT Edge em uma **camada inferior** da hierarquia. Esses dispositivos podem ser um gateway para outros dispositivos de IoT e IoT Edge downstream, mas também precisam rotear qualquer comunicação por meio de seus próprios dispositivos pai.

Algumas arquiteturas de rede exigem que apenas o dispositivo IoT Edge superior em uma hierarquia possa se conectar à nuvem. Nessa configuração, todos os dispositivos IoT Edge nas camadas inferiores de uma hierarquia só podem se comunicar com o dispositivo de gateway (ou pai) e com dispositivos downstream (ou filhos).

Todas as etapas neste artigo se baseiam neles em [configurar um dispositivo IOT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md), que configura um dispositivo IOT Edge para ser um gateway para dispositivos IOT downstream. As mesmas etapas básicas se aplicam a todos os cenários de gateway:

* **Autenticação**: Crie identidades do Hub IOT para todos os dispositivos na hierarquia de gateway.
* **Autorização**: Configure a relação pai/filho no Hub IOT para autorizar que dispositivos filho se conectem ao seu dispositivo pai, como eles se conectaram ao Hub IOT.
* **Descoberta de gateway**: Verifique se o dispositivo filho pode encontrar seu dispositivo pai na rede local.
* **Conexão segura**: estabeleça uma conexão segura com certificados confiáveis que fazem parte da mesma cadeia.

## <a name="prerequisites"></a>Pré-requisitos

* Um hub IoT gratuito ou Standard.
* Pelo menos dois **dispositivos IOT Edge**, um para ser o dispositivo de camada superior e um ou mais dispositivos de camada inferior. Se você não tiver dispositivos IoT Edge disponíveis, poderá [executar Azure IOT Edge em máquinas virtuais Ubuntu](how-to-install-iot-edge-ubuntuvm.md).
* Se você usar o CLI do Azure para criar e gerenciar dispositivos, tenha CLI do Azure v 2.3.1 com a extensão do Azure IoT v 0.10.6 ou superior instalada.

Este artigo fornece etapas e opções detalhadas para ajudá-lo a criar a hierarquia de gateway correta para seu cenário. Para obter um tutorial guiado, consulte [criar uma hierarquia de dispositivos IOT Edge usando gateways](tutorial-nested-iot-edge.md).

## <a name="create-a-gateway-hierarchy"></a>Criar uma hierarquia de gateway

Você cria uma hierarquia de gateway IoT Edge Definindo relações pai/filho para os dispositivos IoT Edge no cenário. Você pode definir um dispositivo pai ao criar uma nova identidade de dispositivo ou pode gerenciar o pai e os filhos de uma identidade de dispositivo existente.

A etapa de configuração de relações pai/filho autoriza os dispositivos filho a se conectarem ao seu dispositivo pai, como eles se conectariam ao Hub IoT.

Somente dispositivos IoT Edge podem ser dispositivos pai, mas os dispositivos IoT Edge e dispositivos IoT podem ser filhos. Um pai pode ter muitos filhos, mas um filho só pode ter um pai. Uma hierarquia de gateway é criada por meio do encadeamento de conjuntos pai/filho juntos para que o filho de um dispositivo seja o pai de outro.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[Portal](#tab/azure-portal)

No portal do Azure, você pode gerenciar a relação pai/filho quando cria novas identidades de dispositivo ou editando os dispositivos existentes.

Ao criar um novo dispositivo IoT Edge, você tem a opção de escolher dispositivos pai e filhos na lista de dispositivos IoT Edge existentes nesse Hub.

1. No [portal do Azure](https://portal.azure.com), navegue até o Hub IOT.
1. Selecione **IOT Edge** no menu de navegação.
1. Selecione **Adicionar um dispositivo IoT Edge**.
1. Juntamente com a definição da ID do dispositivo e das configurações de autenticação, você pode **definir um dispositivo pai** ou **escolher dispositivos filho**.
1. Escolha o dispositivo ou os dispositivos que você deseja como pai ou filho.

Você também pode criar ou gerenciar relações pai/filho para os dispositivos existentes.

1. No [portal do Azure](https://portal.azure.com), navegue até o Hub IOT.
1. Selecione **IOT Edge** no menu de navegação.
1. Selecione o dispositivo que você deseja gerenciar na lista de **dispositivos IOT Edge**.
1. Selecione **definir um dispositivo pai** ou **gerenciar dispositivos filho**.
1. Adicionar ou remover qualquer dispositivo pai ou filho.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

A extensão [Azure-IOT](/cli/azure/ext/azure-iot) para o CLI do Azure fornece comandos para gerenciar seus recursos de IOT. Você pode gerenciar a relação pai/filho de dispositivos IoT e IoT Edge ao criar novas identidades de dispositivo ou ao editar dispositivos existentes.

O conjunto de comandos de [identidade de dispositivo AZ IOT Hub-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) permite que você gerencie as relações pai/filho de um determinado dispositivo.

O `create` comando inclui parâmetros para adicionar dispositivos filhos e definir um dispositivo pai no momento da criação do dispositivo.

Comandos de identidade de dispositivo adicionais, incluindo `add-children` , `list-children` , e `remove-children` ou `get-parent` `set-parent` , permitem que você gerencie as relações pai/filho para os dispositivos existentes.

---

## <a name="prepare-certificates"></a>Preparar certificados

Uma cadeia consistente de certificados deve ser instalada em dispositivos na mesma hierarquia de gateway para estabelecer uma comunicação segura entre si. Cada dispositivo na hierarquia, seja um dispositivo IoT Edge ou um dispositivo IoT folha, precisa de uma cópia do mesmo certificado de autoridade de certificação raiz. Cada dispositivo de IoT Edge na hierarquia usa esse certificado de autoridade de certificação raiz como a raiz para seu certificado de autoridade de certificação do dispositivo.

Com essa configuração, cada dispositivo downstream IoT Edge ou dispositivo IoT folha pode verificar a identidade de seu pai verificando se os edgeHub aos quais eles se conectam têm um certificado de servidor assinado pelo certificado de autoridade de certificação raiz compartilhada.

<!-- TODO: certificate graphic -->

Crie os seguintes certificados:

* Um **certificado de autoridade de certificação raiz**, que é o certificado compartilhado mais alto para todos os dispositivos em uma determinada hierarquia de gateway. Esse certificado está instalado em todos os dispositivos.
* Todos os **certificados intermediários** que você deseja incluir na cadeia de certificados raiz.
* Um **certificado de autoridade de certificação do dispositivo** e sua **chave privada**, gerados pelos certificados raiz e intermediário. Você precisa de um certificado de autoridade de certificação de dispositivo exclusivo para cada dispositivo de IoT Edge na hierarquia de gateway.

Você pode usar uma autoridade de certificação autoassinada ou comprar uma de uma autoridade de certificação comercial confiável como Baltimore, Verisign, DigiCert ou GlobalSign.

Se você não tiver seus próprios certificados para usar, poderá [criar certificados de demonstração para testar IOT Edge recursos de dispositivo](how-to-create-test-certificates.md). Siga as etapas nesse artigo para criar um conjunto de certificados raiz e intermediário e, em seguida, para criar IoT Edge certificados de autoridade de certificação de dispositivo para cada um dos seus dispositivos.

## <a name="configure-iot-edge-on-devices"></a>Configurar IoT Edge em dispositivos

As etapas para configurar IoT Edge como um gateway são muito semelhantes às etapas para configurar IoT Edge como um dispositivo downstream.

Para habilitar a descoberta de gateway, cada dispositivo de gateway IoT Edge precisa ser configurado com um **nome de host** que seus dispositivos filho usarão para localizá-lo na rede local. Cada dispositivo de IoT Edge downstream precisa ser configurado com um **parent_hostname** ao qual se conectar. Se um único dispositivo de IoT Edge for um dispositivo pai e filho, ele precisará dos dois parâmetros.

Para habilitar conexões seguras, cada dispositivo de IoT Edge em um cenário de gateway precisa ser configurado com um certificado de autoridade de certificação de dispositivo exclusivo e uma cópia do certificado de autoridade de certificação raiz compartilhado por todos os dispositivos na hierarquia de gateway.

Você já deve ter IoT Edge instalado em seu dispositivo. Caso contrário, siga as etapas para [registrar um dispositivo IOT Edge no Hub IOT](how-to-register-device.md) e, em seguida, [Instale o Azure IOT Edge Runtime](how-to-install-iot-edge.md).

As etapas nesta seção fazem referência ao **certificado de autoridade de certificação raiz** e ao certificado de AC do dispositivo e à **chave privada** que foram discutidos anteriormente neste artigo. Se você criou esses certificados em um dispositivo diferente, disponibilize-os neste dispositivo. Você pode transferir os arquivos fisicamente, como com uma unidade USB, com um serviço como [Azure Key Vault](../key-vault/general/overview.md)ou com uma função como uma [cópia de arquivo segura](https://www.ssh.com/ssh/scp/).

Use as etapas a seguir para configurar IoT Edge em seu dispositivo.

Verifique se o usuário **iotedge** tem permissões de leitura para o diretório que contém os certificados e as chaves.

1. Instale o **certificado de autoridade de certificação raiz** neste IOT Edge dispositivo.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem
   ```

1. Atualize o repositório de certificados.

   ```bash
   sudo update-ca-certificates
   ```

   Esse comando deve gerar um certificado que foi adicionado ao/etc/SSL/Certs.

1. Abra o arquivo de configuração do IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

   >[!TIP]
   >Se o arquivo de configuração não existir no seu dispositivo ainda, use `/etc/aziot/config.toml.edge.template` como um modelo para criar um.

1. Localize a seção **hostname** no arquivo de configuração. Remova a marca de comentário da linha que contém o `hostname` parâmetro e atualize o valor para ser o FQDN (nome de domínio totalmente qualificado) ou o endereço IP do dispositivo de IOT Edge.

   O valor desse parâmetro é o que os dispositivos downstream usarão para se conectar a esse gateway. Por padrão, o nome do host usa o computador, mas o FQDN ou o endereço IP é necessário para conectar dispositivos downstream.

   Use um nome de host com menos de 64 caracteres, que é o limite de caracteres de um certificado de servidor Name comum.

   Seja consistente com o padrão hostname em uma hierarquia de gateway. Use FQDNs ou endereços IP, mas não ambos.

1. *Se este dispositivo for um dispositivo filho*, localize a seção **nome do host pai** . Remova a marca de comentário e atualize o `parent_hostname` parâmetro para que seja o FQDN ou o endereço IP do dispositivo pai, correspondendo qualquer que fosse fornecido como o nome do host no arquivo de configuração do dispositivo pai.

1. Localize a seção **certificado do pacote de confiança** . Remova a marca de comentário e atualize o `trust_bundle_cert` parâmetro com o URI do arquivo para o certificado de autoridade de certificação raiz em seu dispositivo.

1. Embora esse recurso esteja em visualização pública, você precisa configurar o dispositivo de IoT Edge para usar a versão de visualização pública do agente de IoT Edge quando ele é iniciado.

   Localize a seção **padrão do agente de borda** e atualize o valor da imagem para a imagem de visualização pública:

   ```toml
   [agent.config]
   image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4"
   ```

1. Localize a seção **certificado de autoridade de certificação do Edge** no arquivo de configuração. Remova os comentários das linhas nesta seção e forneça os caminhos de URI de arquivo para o certificado e os arquivos de chave no dispositivo IoT Edge.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Salve ( `Ctrl+O` ) e feche ( `Ctrl+X` ) o arquivo de configuração.

1. Se você tiver usado outros certificados para IoT Edge antes, exclua os arquivos nos dois diretórios a seguir para certificar-se de que os novos certificados sejam aplicados:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

1. Aplique suas alterações.

   ```bash
   sudo iotedge config apply
   ```

1. Verifique se há erros na configuração.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >A ferramenta de verificação de IoT Edge usa um contêiner para executar algumas das verificações de diagnóstico. Se você quiser usar essa ferramenta em dispositivos de IoT Edge downstream, verifique se eles podem acessar `mcr.microsoft.com/azureiotedge-diagnostics:latest` ou se a imagem de contêiner está em seu registro de contêiner privado.

## <a name="configure-runtime-modules-for-public-preview"></a>Configurar módulos de tempo de execução para visualização pública

Embora esse recurso esteja em visualização pública, você precisa configurar seu dispositivo de IoT Edge para usar as versões de visualização pública dos módulos de tempo de execução IoT Edge. A seção anterior fornece as etapas para configurar o edgeAgent na inicialização. Você também precisa configurar os módulos de tempo de execução em implantações para seu dispositivo.

1. Configure o módulo edgeHub para usar a imagem de visualização pública: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4` .

1. Configure as seguintes variáveis de ambiente para o módulo edgeHub:

   | Nome | Valor |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Configure o módulo edgeAgent para usar a imagem de visualização pública: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4` .

## <a name="network-isolate-downstream-devices"></a>Isolar dispositivos downstream de rede

As etapas até o momento neste artigo configuram IoT Edge dispositivos como um gateway ou um dispositivo downstream e criam uma conexão confiável entre eles. O dispositivo de gateway lida com as interações entre o dispositivo downstream e o Hub IoT, incluindo autenticação e roteamento de mensagens. Os módulos implantados em dispositivos de IoT Edge downstream ainda podem criar suas próprias conexões com os serviços de nuvem.

Algumas arquiteturas de rede, como as que seguem o padrão ISA-95, buscam minimizar o número de conexões com a Internet. Nesses cenários, você pode configurar dispositivos de IoT Edge downstream sem conectividade direta com a Internet. Além de rotear as comunicações do Hub IoT por meio de seu dispositivo de gateway, os dispositivos de IoT Edge downstream podem contar com o dispositivo de gateway para todas as conexões de nuvem.

Essa configuração de rede requer que apenas o dispositivo IoT Edge na camada superior de uma hierarquia de gateway tenha conexões diretas com a nuvem. IoT Edge dispositivos nas camadas inferiores só podem se comunicar com seu dispositivo pai ou com dispositivos filhos. Os módulos especiais nos dispositivos de gateway habilitam esse cenário, incluindo:

* O **módulo proxy de API** é necessário em qualquer IOT Edge gateway que tenha outro dispositivo de IOT Edge abaixo dele. Isso significa que ele deve estar em *todas as camadas* de uma hierarquia de gateway, exceto na camada inferior. Esse módulo usa um proxy reverso [Nginx](https://nginx.org) para rotear dados http por meio de camadas de rede em uma única porta. Ele é altamente configurável por meio de seu módulo e de suas variáveis de ambiente, portanto, pode ser ajustado para se adequar aos seus requisitos de cenário de gateway.

* O **módulo do registro do Docker** pode ser implantado no gateway de IOT Edge na *camada superior* de uma hierarquia de gateway. Esse módulo é responsável por recuperar e armazenar em cache as imagens de contêiner em nome de todos os dispositivos IoT Edge em camadas inferiores. A alternativa para implantar esse módulo na camada superior é usar um registro local ou carregar manualmente as imagens de contêiner em dispositivos e definir a política de pull de módulo como **nunca**.

* O **armazenamento de BLOBs do Azure no IOT Edge** pode ser implantado no gateway de IOT Edge na *camada superior* de uma hierarquia de gateway. Esse módulo é responsável por carregar BLOBs em nome de todos os dispositivos IoT Edge em camadas inferiores. A capacidade de carregar BLOBs também permite uma funcionalidade útil de solução de problemas para dispositivos IoT Edge em camadas inferiores, como carregamento de log de módulo e carregamento de pacote de suporte.

### <a name="network-configuration"></a>Configuração de rede

Para cada dispositivo de gateway na camada superior, os operadores de rede precisam:

* Forneça um endereço IP estático ou FQDN (nome de domínio totalmente qualificado).
* Autorize a comunicação de saída deste endereço IP com o nome do host do Hub IoT do Azure nas portas 443 (HTTPS) e 5671 (AMQP).
* Autorize a comunicação de saída deste endereço IP com o nome do host do registro de contêiner do Azure pela porta 443 (HTTPS).

  O módulo proxy de API só pode tratar conexões para um registro de contêiner por vez. É recomendável ter todas as imagens de contêiner, incluindo as imagens públicas fornecidas pelo mcr.microsoft.com (registro de contêiner da Microsoft), armazenadas em seu registro de contêiner privado.

Para cada dispositivo de gateway em uma camada inferior, os operadores de rede precisam:

* Forneça um endereço IP estático.
* Autorize a comunicação de saída desse endereço IP com o endereço IP do gateway pai nas portas 443 (HTTPS) e 5671 (AMQP).

### <a name="deploy-modules-to-top-layer-devices"></a>Implantar módulos em dispositivos de camada superior

O dispositivo IoT Edge na camada superior de uma hierarquia de gateway tem um conjunto de módulos necessários que devem ser implantados nele, além de quaisquer módulos de carga de trabalho que você possa executar no dispositivo.

O módulo de proxy de API foi projetado para ser personalizado para lidar com os cenários de gateway mais comuns. Este artigo fornece e exemplo para configurar os módulos em uma configuração básica. Consulte [Configurar o módulo de proxy de API para seu cenário de hierarquia de gateway](how-to-configure-api-proxy-module.md) para obter informações e exemplos mais detalhados.

1. No [portal do Azure](https://portal.azure.com), navegue até o Hub IOT.
1. Selecione **IOT Edge** no menu de navegação.
1. Selecione o dispositivo de camada superior que você está configurando na lista de **dispositivos IOT Edge**.
1. Selecione **Definir módulos**.
1. Na seção **módulos IOT Edge** , selecione **Adicionar** e escolha **módulo Marketplace**.
1. Pesquise e selecione o módulo de **proxy de API IOT Edge** .
1. Selecione o nome do módulo de proxy de API na lista de módulos implantados e atualize as seguintes configurações de módulo:
   1. Na guia **variáveis de ambiente** , atualize o valor de **NGINX_DEFAULT_PORT** para `443` .
   1. Na guia **Opções de criação de contêiner** , atualize as associações de porta para fazer referência à porta 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Essas alterações configuram o módulo de proxy de API para escutar na porta 443. Para evitar colisões de associação de porta, você precisa configurar o módulo edgeHub para não escutar na porta 443. Em vez disso, o módulo de proxy de API roteará qualquer tráfego edgeHub na porta 443.

1. Selecione **configurações de tempo de execução** e localize as opções de criação de módulo edgeHub. Exclua a associação de porta para a porta 443, deixando as associações para as portas 5671 e 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Selecione **salvar** para salvar as alterações nas configurações de tempo de execução.
1. Selecione **Adicionar** novamente e, em seguida, escolha **IOT Edge módulo**.
1. Forneça os seguintes valores para adicionar o módulo do registro do Docker à sua implantação:
   1. **Nome do módulo de IOT Edge**: `registry`
   1. Na guia **configurações do módulo** , **URI da imagem**: `registry:latest`
   1. Na guia **variáveis de ambiente** , adicione as seguintes variáveis de ambiente:

      * **Name**: `REGISTRY_PROXY_REMOTEURL` **Value**: a URL para o registro de contêiner para o qual você deseja que este módulo do registro seja mapeado. Por exemplo, `https://myregistry.azurecr`.

        O módulo do registro só pode ser mapeado para um registro de contêiner, portanto, é recomendável ter todas as imagens de contêiner em um único registro de contêiner privado.

      * **Name**: `REGISTRY_PROXY_USERNAME` **Value**: username para autenticar no registro de contêiner.

      * **Nome**: `REGISTRY_PROXY_PASSWORD` **valor**: senha para autenticar no registro de contêiner.

   1. Na guia **Opções de criação do contêiner** , Cole:

      ```json
      {
          "HostConfig": {
              "PortBindings": {
                  "5000/tcp": [
                      {
                          "HostPort": "5000"
                      }
                  ]
              }
          }
      }
      ```

1. Selecione **Adicionar** para adicionar o módulo à implantação.
1. Selecione **Avançar: rotas** para ir para a próxima etapa.
1. Para habilitar mensagens do dispositivo para a nuvem de dispositivos downstream para acessar o Hub IoT, inclua uma rota que passe todas as mensagens para o Hub IoT. Por exemplo:
    1. **Nome**: `Route`
    1. **Valor**: `FROM /messages/* INTO $upstream`
1. Selecione **revisar + criar** para ir para a etapa final.
1. Selecione **criar** para implantar em seu dispositivo.

### <a name="deploy-modules-to-lower-layer-devices"></a>Implantar módulos em dispositivos de camada inferior

IoT Edge dispositivos em camadas inferiores de uma hierarquia de gateway têm um módulo necessário que deve ser implantado neles, além de quaisquer módulos de carga de trabalho que você possa executar no dispositivo.

#### <a name="route-container-image-pulls"></a>Pull da imagem de contêiner de rota

Antes de discutir o módulo de proxy necessário para dispositivos IoT Edge em hierarquias de gateway, é importante entender como IoT Edge dispositivos em camadas inferiores obtêm suas imagens de módulo.

Se os dispositivos de camada inferior não puderem se conectar à nuvem, mas você quiser que eles recebam imagens de módulo como de costume, o dispositivo de camada superior da hierarquia de gateway deverá ser configurado para lidar com essas solicitações. O dispositivo de camada superior precisa executar um módulo **do registro do** Docker que é mapeado para o registro de contêiner. Em seguida, configure o módulo de proxy de API para rotear solicitações de contêiner para ele. Esses detalhes são discutidos nas seções anteriores deste artigo. Nessa configuração, os dispositivos de camada inferior não devem apontar para registros de contêiner de nuvem, mas para o registro em execução na camada superior.

Por exemplo, em vez de chamar `mcr.microsoft.com/azureiotedge-api-proxy:latest` , dispositivos de camada inferiores devem chamar `$upstream:443/azureiotedge-api-proxy:latest` .

O parâmetro **$upstream** aponta para o pai de um dispositivo de camada inferior, de modo que a solicitação será roteada por todas as camadas até atingir a camada superior, que tem um ambiente de proxy roteando solicitações de contêiner para o módulo do registro. A `:443` porta neste exemplo deve ser substituída por qualquer porta que o módulo de proxy de API no dispositivo pai esteja escutando.

O módulo proxy de API só pode rotear para um módulo do registro e cada módulo do registro só pode ser mapeado para um registro de contêiner. Portanto, todas as imagens que reduzem os dispositivos de camada precisam ser armazenadas em um único registro de contêiner.

Se você não quiser que dispositivos de camada inferior façam solicitações pull de módulo por meio de uma hierarquia de gateway, outra opção é gerenciar uma solução de registro local. Ou envie as imagens do módulo para os dispositivos antes de criar implantações e, em seguida, defina **imagePullPolicy** como **nunca**.

#### <a name="bootstrap-the-iot-edge-agent"></a>Inicializar o agente de IoT Edge

O agente de IoT Edge é o primeiro componente de tempo de execução a ser iniciado em qualquer dispositivo de IoT Edge. Você precisa verificar se os dispositivos downstream IoT Edge podem acessar a imagem do módulo edgeAgent quando eles são iniciados e, em seguida, podem acessar as implantações e iniciar o restante das imagens do módulo.

Quando você entra no arquivo de configuração em um dispositivo IoT Edge para fornecer suas informações de autenticação, certificados e nome do host pai, também atualiza a imagem de contêiner edgeAgent.

Se o dispositivo de gateway de nível superior estiver configurado para lidar com solicitações de imagem de contêiner, substitua `mcr.microsoft.com` pelo nome de host pai e pela porta de escuta de proxy de API. No manifesto de implantação, você pode usar `$upstream` como um atalho, mas isso requer que o módulo edgeHub manipule o roteamento e esse módulo não tenha começado neste ponto. Por exemplo:

```toml
[agent]
name = "edgeAgent"
type = "docker"

[agent.config]
image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2.0-rc4"
```

Se você estiver usando um registro de contêiner local ou fornecendo as imagens de contêiner manualmente no dispositivo, atualize o arquivo de configuração adequadamente.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>Configurar tempo de execução e implantar o módulo de proxy

O **módulo de proxy de API** é necessário para rotear todas as comunicações entre a nuvem e os dispositivos de IOT Edge downstream. Um dispositivo IoT Edge na camada inferior da hierarquia, sem dispositivos de IoT Edge downstream, não precisa desse módulo.

O módulo de proxy de API foi projetado para ser personalizado para lidar com os cenários de gateway mais comuns. Este artigo aborda brevemente as etapas para configurar os módulos em uma configuração básica. Consulte [Configurar o módulo de proxy de API para seu cenário de hierarquia de gateway](how-to-configure-api-proxy-module.md) para obter informações e exemplos mais detalhados.

1. No [portal do Azure](https://portal.azure.com), navegue até o Hub IOT.
1. Selecione **IOT Edge** no menu de navegação.
1. Selecione o dispositivo de camada inferior que você está configurando na lista de **dispositivos IOT Edge**.
1. Selecione **Definir módulos**.
1. Na seção **módulos IOT Edge** , selecione **Adicionar** e escolha **módulo Marketplace**.
1. Pesquise e selecione o módulo de **proxy de API IOT Edge** .
1. Selecione o nome do módulo de proxy de API na lista de módulos implantados e atualize as seguintes configurações de módulo:
   1. Na guia **configurações do módulo** , atualize o valor do **URI da imagem**. Substitua `mcr.microsoft.com` por `$upstream:443`.
   1. Na guia **variáveis de ambiente** , atualize o valor de **NGINX_DEFAULT_PORT** para `443` .
   1. Na guia **Opções de criação de contêiner** , atualize as associações de porta para fazer referência à porta 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Essas alterações configuram o módulo de proxy de API para escutar na porta 443. Para evitar colisões de associação de porta, você precisa configurar o módulo edgeHub para não escutar na porta 443. Em vez disso, o módulo de proxy de API roteará qualquer tráfego edgeHub na porta 443.

1. Selecione **configurações de tempo de execução**.
1. Atualize as configurações do módulo edgeHub:

   1. No campo **imagem** , substitua `mcr.microsoft.com` por `$upstream:443` .
   1. No campo **criar opções** , exclua a associação de porta para a porta 443, deixando as associações para as portas 5671 e 8883.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Atualize as configurações do módulo edgeAgent:
   1. No campo **imagem** , substitua `mcr.microsoft.com` por `$upstream:443` .

1. Selecione **salvar** para salvar as alterações nas configurações de tempo de execução.
1. Selecione **Avançar: rotas** para ir para a próxima etapa.
1. Para habilitar mensagens do dispositivo para a nuvem de dispositivos downstream para acessar o Hub IoT, inclua uma rota que passe todas as mensagens para `$upstream` . O parâmetro upstream aponta para o dispositivo pai no caso de dispositivos de camada inferior. Por exemplo:
    1. **Nome**: `Route`
    1. **Valor**: `FROM /messages/* INTO $upstream`
1. Selecione **revisar + criar** para ir para a etapa final.
1. Selecione **criar** para implantar em seu dispositivo.

## <a name="next-steps"></a>Próximas etapas

[Como um dispositivo IoT Edge pode ser usado como um gateway](iot-edge-as-gateway.md)

[Configurar o módulo de proxy de API para seu cenário de hierarquia de gateway](how-to-configure-api-proxy-module.md)