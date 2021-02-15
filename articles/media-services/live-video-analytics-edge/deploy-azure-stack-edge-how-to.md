---
title: Implantar a Análise Dinâmica de Vídeo no Azure Stack Edge
description: Este artigo lista as etapas que ajudarão você a implantar a análise de vídeo ao vivo em seu Azure Stack Edge.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: cc3dcfaa96034e807d3d82e75eedc0f6a82eff08
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551001"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>Implantar a Análise Dinâmica de Vídeo no Azure Stack Edge

Este artigo lista as etapas que ajudarão você a implantar a análise de vídeo ao vivo em seu Azure Stack Edge. Depois que o dispositivo tiver sido configurado e ativado, ele estará pronto para a implantação da análise de vídeo ao vivo. 

Para a análise de vídeo ao vivo, implantaremos por meio do Hub IoT, mas os Azure Stack recursos de borda expõem uma API kubernetes, que permite ao cliente implantar soluções de reconhecimento de Hub não IoT adicionais que podem ser modeladas com análise de vídeo ao vivo. 

> [!TIP]
> Usar a API kubernetes (K8s) para implantação personalizada é um caso avançado. É recomendável que o cliente crie módulos de borda e implante por meio do Hub IoT para cada Azure Stack recurso de borda em vez de usar a API kubernetes. Neste artigo, mostraremos as etapas de implantação do módulo análise de vídeo ao vivo usando o Hub IoT.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure à qual você tem [privilégios de proprietário](../../role-based-access-control/built-in-roles.md#owner).
* Um recurso do [Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-prep.md)
   
* [Um hub IoT](../../iot-hub/iot-hub-create-through-portal.md)
* Uma [entidade de serviço](./create-custom-azure-resource-manager-role-how-to.md#create-service-principal) para o módulo análise de vídeo ao vivo.

   Use uma destas regiões em que o Hub IoT está disponível: leste dos EUA 2, EUA Central, norte EUA Central, leste do Japão, oeste dos EUA 2, Oeste EUA Central, leste do Canadá, Sul do Reino Unido, França central, sul da França, Norte da Suíça, Oeste da Suíça e oeste do Japão.
* Conta de armazenamento

    É recomendável que você use contas de armazenamento de uso geral v2 (GPv2).  
    Saiba mais sobre uma [conta de armazenamento de uso geral v2](../../storage/common/storage-account-upgrade.md?tabs=azure-portal).
* O [Visual Studio Code](https://code.visualstudio.com/) em seu computador de desenvolvimento. Verifique se você tem a [Extensão do Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Verifique se a rede à qual seu computador de desenvolvimento está conectado permite o Advanced Message Queueing Protocol pela porta 5671. Essa configuração permite que o Azure IoT Tools se comunique com o Hub IoT do Azure.

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>Configurando o Azure Stack Edge para usar a análise de vídeo ao vivo

O Azure Stack Edge é uma solução de hardware como serviço e um dispositivo de computação de borda habilitado para ia com recursos de transferência de dados de rede. Leia mais sobre [Azure Stack borda e instruções de configuração detalhadas](../../databox-online/azure-stack-edge-deploy-prep.md). Para começar, siga as instruções nos links abaixo:

* [Azure Stack a criação de recursos de borda/Gateway do Data Box](../../databox-online/azure-stack-edge-deploy-prep.md)
* [Instalar e configurar](../../databox-online/azure-stack-edge-deploy-install.md)
* [Conexão e ativação](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md)
* [Anexar um hub IoT ao Azure Stack Edge](https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-deploy-configure-compute#configure-compute)
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Habilitar pré-requisitos de computação na interface do usuário local do Azure Stack Edge

Antes de continuar, verifique se:

* Você ativou seu Azure Stack recurso de borda.
* Você tem acesso a um sistema de cliente Windows executando o PowerShell 5,0 ou posterior para acessar o recurso de borda do Azure Stack.
* Para implantar um cluster kubernetes, você precisa configurar o recurso de borda do Azure Stack por meio de sua [interface do usuário da Web local](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup). 
    
    * Para habilitar a computação, na interface do usuário da Web local do seu dispositivo, vá para a página computação.
    
        * Selecione uma interface de rede que você deseja habilitar para computação. Selecione Habilitar. Habilitar resultados de computação na criação de um comutador virtual em seu dispositivo nessa interface de rede.
        * Deixe os IPs do nó de teste do kubernetes e os IPs de serviços externos do kubernetes em branco.
        * Selecione aplicar – esta operação deve levar cerca de 2 minutos.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text=" Pré-requisitos de computação na interface do usuário local do Azure Stack Edge":::

        * Se o DNS não estiver configurado para a API kubernetes e Azure Stack recurso do Edge, você poderá atualizar o arquivo de host da janela.
        
            * Abrir um editor de texto como administrador
            * Abrir o arquivo ' para C:\Windows\System32\drivers\etc\hosts '
            * Adicione o IPv4 e o nome do host do dispositivo da API kubernetes ao arquivo. (Essas informações podem ser encontradas no portal do Azure Stack Edge na seção dispositivos.)
            * Salvar e fechar

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>Implantar módulo de borda de análise de vídeo ao vivo usando portal do Azure

Para isso, vamos apenas executar etapas específicas da [implantação de análise de vídeo ao vivo por meio do Hub IOT](deploy-iot-edge-device.md).

1. Ignore as etapas de criação de usuário e grupo e vá para implantar módulo de [borda de análise de vídeo ao vivo](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module) . Siga as etapas mencionadas aqui.
1. Em opções de criação de contêiner, você não precisa definir variáveis de ambiente. Então, pule esta etapa.
1. Abra a guia Opções de criação do contêiner.

   * Copie e cole o JSON a seguir na caixa, para limitar o tamanho dos arquivos de log produzidos pelo módulo.
    
      ```json
        "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                        "max-size": "10m",
                        "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > Se estiver usando o protocolo gRPC com transferência de memória compartilhada, use o modo IPC do host para acesso de memória compartilhada entre soluções de inferência e análise de vídeo ao vivo.
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > A seção "Bindes" no JSON tem 2 entradas. Os diretórios mencionados na seção de ligação acima serão criados automaticamente pelo LVA.  
        Sinta-se à vontade para atualizar as associações de dispositivo de borda, mas se você fizer isso, verifique se esses diretórios existem no dispositivo.
    
    * "/var/lib/azuremediaservices:/var/lib/azuremediaservices": é usado para associar os dados de configuração de aplicativo persistentes do contêiner e armazená-los no dispositivo de borda.
    * "/var/media:/var/media": isso associa as pastas de mídia entre o dispositivo de borda e o contêiner. Isso é usado para armazenar as gravações de vídeo quando você executa uma topologia de mídia gráfica que dá suporte ao armazenamento de clipes de vídeo no dispositivo de borda.
        
1. Continue as etapas no documento e preencha as configurações de configuração do módulo.
1. Selecione **Avançar**: rotas para continuar na seção rotas. Especifique as rotas.

    Mantenha as rotas padrão e selecione Avançar: revisar + criar para continuar na seção revisão.
1. [Revisar e verificar sua implantação](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a>Adicional Configurar montagens de volume do Docker

Se você quiser exibir os dados nos diretórios de trabalho, siga estas etapas para configurar as montagens de volume do Docker antes da implantação. 

Estas etapas abrangem a criação de um usuário de gateway e a configuração de compartilhamentos de arquivos para exibir o conteúdo do diretório de trabalho de análise de vídeo ao vivo e a pasta de mídia de análise de vídeo ao

> [!NOTE]
> As montagens de associação têm suporte, mas as montagens de volume permitem que os dados sejam exibidos e, se desejado, sejam copiados remotamente. É possível usar montagens de ligação e de volume, mas elas não podem apontar para o mesmo caminho de contêiner.

1. Abra portal do Azure e vá para o recurso de borda do Azure Stack.
1. Crie um **usuário de gateway** que possa acessar compartilhamentos.
    
    1. No painel de navegação esquerdo, clique em **Gateway de armazenamento em nuvem**.
    1. Clique em **usuários** no painel de navegação à esquerda.
    1. Clique em íon **+ Adicionar usuário** para definir o nome de usuário e a senha. (Recomendado: `lvauser` ).
    1. Clique em **Adicionar**.
    
1. Crie um **compartilhamento local** para persistência de análise de vídeo ao vivo.

    1. Clique em **armazenamento em nuvem gateway->compartilhamentos**.
    1. Clique em **+ Adicionar compartilhamentos**.
    1. Defina um nome de compartilhamento. (Recomendado: `lva` ).
    1. Mantenha o tipo de compartilhamento como SMB.
    1. Verifique se **a opção usar o compartilhamento com a computação de borda** está marcada.
    1. Verifique se **configurar como compartilhamento local de borda** está marcado.
    1. Em detalhes do usuário, conceda acesso ao compartilhamento para o usuário criado recentemente.
    1. Clique em **criar**.
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="Compartilhamento local":::  

    > [!TIP]
    > Usando seu cliente Windows conectado ao seu Azure Stack Edge, conecte-se aos compartilhamentos SMB seguindo as etapas [mencionadas neste documento](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share).    

1. Crie um compartilhamento remoto para o armazenamento de sincronização de arquivos.

    1. Primeiro, crie uma conta de armazenamento de BLOBs na mesma região clicando em **Cloud Storage gateway->contas de armazenamento**.
    1. Clique em **armazenamento em nuvem gateway->compartilhamentos**.
    1. Clique em **+ Adicionar compartilhamentos**.
    1. Defina um nome de compartilhamento. (Recomendado: mídia).
    1. Mantenha o tipo de compartilhamento como SMB.
    1. Verifique se **a opção usar o compartilhamento com a computação de borda** está marcada.
    1. Verifique se **configurar como compartilhamento local de borda** não está marcado.
    1. Selecione a conta de armazenamento criada recentemente.
    1. Defina um nome de contêiner.
    1. Defina o tipo de armazenamento como blob de blocos.
    1. Em detalhes do usuário, conceda acesso ao compartilhamento para o usuário criado recentemente.
    1. Clique em **criar**.    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="Compartilhamento remoto":::
    
    
1. Atualize as opções de criação de contêiner do módulo de borda de análise de vídeo ao vivo (consulte o ponto 4 no [documento adicionar módulos](deploy-iot-edge-device.md#add-modules)) para usar montagens de volume.

   ```json
      "createOptions": 
         {
             "HostConfig": 
             {
                 "Binds": 
                 [
                     "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                 ],
                 "Mounts": 
                 [
                     {
                         "Target": "/var/media",
                         "Source": "media",
                         "Type": "volume"
                     }
                 ]
             }
         }
    ```

### <a name="verify-that-the-module-is-running"></a>Verifique se o módulo está em execução

A etapa final é garantir que o módulo esteja conectado e funcionando como esperado. O status de tempo de execução do módulo deve estar em execução para seu dispositivo IoT Edge no recurso do Hub IoT.

Para verificar se o módulo está em execução, faça o seguinte:

1. Na portal do Azure, retorne ao recurso de borda do Azure Stack
1. Selecione o bloco módulos. Isso direcionará você para a folha Módulos. Na lista de módulos, identifique o módulo implantado. O status do runtime do módulo adicionado deve ser em execução.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="Módulo personalizado":::

### <a name="configure-the-azure-iot-tools-extension"></a>Configurar a extensão do Azure IoT Tools

Siga estas instruções para se conectar ao seu hub IoT usando a extensão do Azure IoT Tools.

1. Em Visual Studio Code, selecione Exibir > Explorer. Ou selecione Ctrl+Shift+E.
1. No canto inferior esquerdo da guia Explorer, selecione Hub IoT do Azure.
1. Selecione o ícone Mais opções para ver o menu de contexto. Em seguida, selecione Definir cadeia de conexão do Hub IoT.
1. Quando uma caixa de diálogo de entrada aparecer, insira a cadeia de conexão do Hub IoT. 

   * Para obter a cadeia de conexão, vá para o Hub IoT em portal do Azure e clique em políticas de acesso compartilhado no painel de navegação esquerdo.
   * Clique em iothubowner obter as chaves de acesso compartilhado.
   * Copie a cadeia de conexão – Primary Key e cole-a na caixa de entrada no VSCode.

   A cadeia de conexão terá a seguinte aparência:<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Se a conexão for bem-sucedida, a lista de dispositivos de borda será mostrada. Você deve ver seu Azure Stack Edge. Agora, você pode gerenciar seus dispositivos do IoT Edge e interagir com o Hub IoT do Azure por meio do menu de contexto. Para exibir os módulos implantados no dispositivo de borda, no dispositivo Azure Stack, expanda o nó módulos.
    
## <a name="troubleshooting"></a>Solução de problemas

* Kubectl (kubernetes API Access).

    * Siga a documentação para configurar seu computador para [acessar o cluster kubernetes](https://review.docs.microsoft.com/azure/databox-online/azure-stack-edge-j-series-create-kubernetes-cluster?toc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Ftoc.json&bc=%2Fazure%2Fdatabox-online%2Fazure-stack-edge-gpu%2Fbreadcrumb%2Ftoc.json&branch=release-tzl#debug-kubernetes-issues).
    * Todos os módulos implantados IoT Edge usam o `iotedge` namespace. Certifique-se de incluir isso ao usar o kubectl.
* Logs de módulo

    A `iotedge` ferramenta não está acessível para obter logs. Você deve usar [os logs do kubectl](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs)  para exibir os logs ou o pipe para um arquivo. Exemplo: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`
* Métricas do pod e do nó

    Use o [kubectl Top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)  para ver as métricas de Pod e nó. (Essa funcionalidade estará disponível na próxima versão de borda do Azure Stack. >v2007)<br/>`kubectl top pods -n iotedge`
* Sistema de rede de módulo para a descoberta de módulo no Azure Stack Edge, é necessário que o módulo tenha a associação de porta do host em criaroptions. O módulo será endereçável `moduleName:hostport` .
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* Montagem de volume

    Um módulo não será iniciado se o contêiner estiver tentando montar um volume em um diretório existente e não vazio.
* Memória compartilhada

    A memória compartilhada em Azure Stack recursos de borda tem suporte em pods em qualquer namespace usando IPC do host.
    Configurando a memória compartilhada em um módulo de borda para implantação por meio do Hub IoT.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    (Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API.
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
* Avançadas Colocal de Pod

    Ao usar o K8s para implantar soluções de inferência personalizadas que se comunicam com a análise de vídeo ao vivo via gRPC, você precisa garantir que os pods sejam implantados nos mesmos nós que os módulos de análise de vídeo ao vivo.

    * Opção 1-Use a afinidade de nó e os rótulos de nó internos para colocalização.

    Atualmente, a configuração personalizada NodeSelector não parece ser uma opção, pois os usuários não têm acesso para definir rótulos nos nós. No entanto, dependendo da topologia do cliente e das convenções de nomenclatura, elas podem ser capazes de usar [Rótulos de nós internos](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels). Uma seção nodeAffinity que faz referência a recursos de borda Azure Stack com análise de vídeo ao vivo pode ser adicionada ao manifesto pod de inferência para obter colocalização.
    * Opção 2-Use a afinidade de pod para colocalização (recomendado).
Kubernetes tem suporte para [afinidade de Pod](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)  que pode agendar pods no mesmo nó. Uma seção podAffinity que faz referência ao módulo análise de vídeo ao vivo pode ser adicionada ao manifesto pod de inferência para obter colocalização.

    ```json   
    // Example Live Video Analytics module deployment match labels
    selector:
      matchLabels:
        net.azure-devices.edge.deviceid: dev-ase-1-edge
        net.azure-devices.edge.module: mediaedge
    
    // Example Inference deployment manifest pod affinity
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: net.azure-devices.edge.module
                operator: In
                values:
                - mediaedge
            topologyKey: "kubernetes.io/hostname"
    ```

## <a name="next-steps"></a>Próximas etapas

Você pode usar o módulo para analisar transmissões de vídeo ao vivo invocando os métodos diretos dele. [Invoque os métodos diretos](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls) no módulo.