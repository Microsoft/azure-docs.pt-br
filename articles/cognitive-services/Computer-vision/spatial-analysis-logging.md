---
title: Telemetria e registro em log para contêineres de análise espacial
titleSuffix: Azure Cognitive Services
description: A análise espacial fornece a cada contêiner uma configuração comum de informações de estrutura, registro em log e segurança.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: dda3ece27fd2c687647e0aa289bd1596a87b274f
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98186015"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetria e solução de problemas

A análise espacial inclui um conjunto de recursos para monitorar a integridade do sistema e ajudar a diagnosticar problemas.

## <a name="enable-visualizations"></a>Habilitar visualizações

Para habilitar uma visualização dos eventos do AI insights em um quadro de vídeo, você precisa usar a `.debug` versão de uma [operação de análise espacial](spatial-analysis-operations.md) em um computador desktop. A visualização não é possível em dispositivos Azure Stack Edge. Há quatro operações de depuração disponíveis.

Se o dispositivo não for um dispositivo Azure Stack Edge, edite o arquivo de manifesto de implantação para [computadores desktop](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) para usar o valor correto para a `DISPLAY` variável de ambiente. Ele precisa corresponder à `$DISPLAY` variável no computador host. Depois de atualizar o manifesto de implantação, reimplante o contêiner.

Após a conclusão da implantação, talvez seja necessário copiar o `.Xauthority` arquivo do computador host para o contêiner e reiniciá-lo. No exemplo a seguir, `peopleanalytics` é o nome do contêiner no computador host.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Coletar telemetria de integridade do sistema

Telegraf é uma imagem de código aberto que funciona com análise espacial e está disponível no registro de contêiner da Microsoft. Ele usa as entradas a seguir e as envia para Azure Monitor. O módulo Telegraf pode ser compilado com saídas e entradas personalizadas desejadas. A configuração do módulo Telegraf na análise espacial faz parte do manifesto de implantação (vinculado acima). Esse módulo é opcional e pode ser removido do manifesto se você não precisar dele. 

Entradas: 
1. Métricas de análise espacial
2. Métricas de disco
3. Métricas de CPU
4. Métricas do Docker
5. Métricas de GPU

Saídas:
1. Azure Monitor

O módulo Telegraf de análise espacial fornecido publicará todos os dados de telemetria emitidos pelo contêiner de análise espacial para Azure Monitor. Consulte a [Azure monitor](../../azure-monitor/overview.md) para obter informações sobre como adicionar Azure monitor à sua assinatura.

Depois de configurar Azure Monitor, você precisará criar credenciais que permitam que o módulo envie telemetria. Você pode usar o portal do Azure para criar uma nova entidade de serviço ou usar o comando CLI do Azure abaixo para criar uma.

> [!NOTE] 
> Este comando exige que você tenha privilégios de proprietário na assinatura. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/...”
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

No manifesto de implantação para seu [dispositivo Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [computador desktop](https://go.microsoft.com/fwlink/?linkid=2152270)ou [VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189), procure o módulo *Telegraf* e substitua os valores a seguir pelas informações da entidade de serviço da etapa anterior e reimplante.

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

Depois que o módulo Telegraf é implantado, as métricas relatadas podem ser acessadas por meio do serviço Azure Monitor ou selecionando **monitoramento** no Hub IoT no portal do Azure.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Relatório de telemetria Azure Monitor":::

### <a name="system-health-events"></a>Eventos de integridade do sistema

| Nome do evento | Descrição|
|------|---------|
|archon_exit    |Enviado quando um usuário altera o status do módulo de análise espacial de *em execução* para *parado*.  |
|archon_error   |Enviado quando qualquer um dos processos dentro do contêiner falhar. Esse é um erro crítico.  |
|InputRate  |A taxa na qual o grafo processa a entrada de vídeo. Relatado a cada 5 minutos. | 
|OutputRate     |A taxa na qual o grafo gera informações de AI. Relatado a cada 5 minutos. |
|archon_allGraphsStarted | Enviado quando todos os gráficos concluíram a inicialização. |
|archon_configchange    | Enviado quando uma configuração de grafo é alterada. |
|archon_graphCreationFailed     |Enviado quando o grafo com o relatado `graphId` não é iniciado. |
|archon_graphCreationSuccess    |Enviado quando o grafo com o relatado `graphId` é iniciado com êxito. |
|archon_graphCleanup    | Enviado quando o grafo com o relatado é `graphId` limpo e encerrado. |
|archon_graphHeartbeat  |Pulsação enviada a cada minuto para cada grafo de uma habilidade. |
|archon_apiKeyAuthFail |Enviado quando a chave de recurso de Pesquisa Visual Computacional falha ao autenticar o contêiner por mais de 24 horas, devido aos seguintes motivos: sem cota, inválida, offline. |
|VideoIngesterHeartbeat     |Enviado a cada hora para indicar que o vídeo é transmitido da fonte de vídeo, com o número de erros nessa hora. Relatado para cada grafo. |
|VideoIngesterState | Relatórios *interrompidos* ou *iniciados* para streaming de vídeo. Relatado para cada grafo. |

##  <a name="troubleshooting-an-iot-edge-device"></a>Solução de problemas de um dispositivo IoT Edge

Você pode usar `iotedge` a ferramenta de linha de comando para verificar o status e os logs dos módulos em execução. Por exemplo:
* `iotedge list`: Relata uma lista de módulos em execução. 
  Você pode verificar se há erros com o `iotedge logs edgeAgent` . Se `iotedge` ficar preso, você poderá tentar reiniciá-lo com `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` para reiniciar um módulo específico 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Coletar arquivos de log com o contêiner de diagnóstico

A análise espacial gera logs de depuração do Docker que você pode usar para diagnosticar problemas de tempo de execução ou incluir tíquetes de suporte. O módulo de diagnóstico de análise espacial está disponível no registro de contêiner da Microsoft para você baixar. No arquivo de implantação de manifesto para seu [Azure Stack dispositivo de borda](https://go.microsoft.com/fwlink/?linkid=2142179), [computador desktop](https://go.microsoft.com/fwlink/?linkid=2152270)ou [VM do Azure com a GPU](https://go.microsoft.com/fwlink/?linkid=2152189) , procure o módulo *diagnóstico* .

Na seção "env", adicione a seguinte configuração:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Para otimizar os logs carregados em um ponto de extremidade remoto, como o armazenamento de BLOBs do Azure, recomendamos manter um tamanho de arquivo pequeno. Consulte o exemplo abaixo para obter a configuração de logs do Docker recomendada.

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>Configurar o nível de log

A configuração de nível de log permite controlar o detalhamento dos logs gerados. Os níveis de log com suporte são: `none` , `verbose` , `info` , `warning` e `error` . O nível de detalhe de log padrão para ambos os nós e a plataforma é `info` . 

Os níveis de log podem ser modificados globalmente definindo a `ARCHON_LOG_LEVEL` variável de ambiente para um dos valores permitidos.
Ele também pode ser definido por meio do documento do módulo de IoT Edge, seja globalmente, para todas as habilidades implantadas ou para cada habilidade específica, definindo os valores de `platformLogLevel` e `nodeLogLevel` conforme mostrado abaixo.

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodeLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>coletando logs

> [!NOTE]
> O `diagnostics` módulo não afeta o conteúdo de log, ele só ajuda a coletar, filtrar e carregar logs existentes.
> Você deve ter a API do Docker versão 1,40 ou superior para usar este módulo.

O arquivo de manifesto de implantação de exemplo para seu [dispositivo Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [computador desktop](https://go.microsoft.com/fwlink/?linkid=2152270)ou [VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189) inclui um módulo chamado `diagnostics` que coleta e carrega logs. Esse módulo é desabilitado por padrão e deve ser habilitado por meio da configuração do módulo IoT Edge quando você precisa acessar os logs. 

A `diagnostics` coleção é sob demanda e controlada por meio de um método direto IOT Edge e pode enviar logs para um armazenamento de BLOBs do Azure.

### <a name="configure-diagnostics-upload-targets"></a>Configurar destinos de upload de diagnóstico

No portal de IoT Edge, selecione o dispositivo e, em seguida, o módulo **diagnóstico** . No arquivo de manifesto de implantação de exemplo para seu [dispositivo Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [computadores desktop](https://go.microsoft.com/fwlink/?linkid=2152270)ou [VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189) , procure a seção **variáveis de ambiente** para diagnóstico, chamado `env` e adicione as seguintes informações:

**Configurar upload para o armazenamento de BLOBs do Azure**

1. Crie sua própria conta de armazenamento de BLOBs do Azure, caso ainda não tenha feito isso.
2. Obtenha a **cadeia de conexão** da sua conta de armazenamento do portal do Azure. Ele estará localizado em **chaves de acesso**.
3. Os logs de análise espacial serão automaticamente carregados em um contêiner de armazenamento de BLOBs chamado *rtcvlogs* com o seguinte formato de nome de arquivo: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` .

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Carregando logs de análise espacial

Os logs são carregados sob demanda com o `getRTCVLogs` método IOT Edge, no `diagnostics` módulo. 


1. Vá para a página do portal do Hub IoT, selecione **dispositivos de borda** e, em seguida, selecione o dispositivo e o módulo de diagnóstico. 
2. Vá para a página de detalhes do módulo e clique na guia **_método direto_* _.
3. Digite `getRTCVLogs` o nome do método e uma cadeia de caracteres de formato JSON na carga. Você pode inserir `{}` , que é uma carga vazia. 
4. Defina a conexão e os tempos limite do método e clique em _ * invocar método * *.
5. Selecione o contêiner de destino e crie uma cadeia de caracteres JSON de carga usando os parâmetros descritos na seção de **sintaxe de log** . Clique em **invocar método** para executar a solicitação.

>[!NOTE]
> Invocar o `getRTCVLogs` método com uma carga vazia retornará uma lista de todos os contêineres implantados no dispositivo. O nome do método diferencia maiúsculas de minúsculas. Você receberá um erro 501 se um nome de método incorreto for fornecido.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="Invocando o método getRTCVLogs ":::
![página de método direto do getRTCVLogs](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Sintaxe de log

A tabela abaixo lista os parâmetros que você pode usar ao consultar logs.

| Palavra-chave | Descrição | Valor padrão |
|--|--|--|
| StartTime | Hora de início dos logs desejados, em milissegundos UTC. | `-1`, o início do tempo de execução do contêiner. Quando `[-1.-1]` é usado como um intervalo de tempo, a API retorna logs da última hora.|
| EndTime | Hora de término dos logs desejados, em milissegundos UTC. | `-1`, a hora atual. Quando `[-1.-1]` o intervalo de tempo é usado, a API retorna logs da última hora. |
| ContainerId | Contêiner de destino para buscar logs.| `null`, quando não há nenhuma ID de contêiner. A API retorna todas as informações de contêineres disponíveis com IDs.|
| DoPost | Execute a operação de carregamento. Quando definido como `false` , ele executa a operação solicitada e retorna o tamanho do carregamento sem executar o carregamento. Quando definido como `true` , ele iniciará o carregamento assíncrono dos logs selecionados | `false`, não carregar.|
| Restrição | Indicar quantas linhas de logs carregar por lote | `1000`, Use esse parâmetro para ajustar a velocidade da postagem. |
| Filtros | Filtra os logs a serem carregados | `null`, os filtros podem ser especificados como pares chave-valor com base na estrutura de logs de análise espacial: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` . Por exemplo: `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

A tabela a seguir lista os atributos na resposta da consulta.

| Palavra-chave | Descrição|
|--|--|
|DoPost| *True* ou *false*. Indica se os logs foram carregados ou não. Quando você opta por não carregar logs, a API retorna informações * de forma **síncrona**. Quando você optar por carregar os logs, a API retornará 200, se a solicitação for válida, e iniciará o carregamento de logs de _*_forma assíncrona_*_.|
|Filtro de time-| Filtro de tempo aplicado aos logs.|
|ValueFilters| Filtros de palavras-chave aplicados aos logs. |
|TimeStamp| Hora de início da execução do método. |
|ContainerId| ID do contêiner de destino. |
|FetchCounter| Número total de linhas de log. |
|FetchSizeInByte| Quantidade total de dados de log em bytes. |
|MatchCounter| Número válido de linhas de log. |
|MatchSizeInByte| Quantidade válida de dados de log em bytes. |
|FilterCount| Número total de linhas de log após a aplicação do filtro. |
|FilterSizeInByte| Quantidade total de dados de log em bytes após a aplicação do filtro. |
|FetchLogsDurationInMiliSec| Duração da operação de busca. |
|PaseLogsDurationInMiliSec| Duração da operação de filtro. |
|PostLogsDurationInMiliSec| Duração da operação post. |

#### <a name="example-request"></a>Solicitação de exemplo 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>Exemplo de resposta 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

Verifique as linhas, os horários e os tamanhos do log de busca. se essas configurações forem boas, substitua _*_doPost_*_ para `true` e isso enviará por push os logs com os mesmos filtros para os destinos. 

Você pode exportar logs do armazenamento de BLOBs do Azure ao solucionar problemas. 

## <a name="common-issues"></a>Problemas comuns

Se você vir a seguinte mensagem nos logs de módulo, pode significar que sua assinatura do Azure precisa ser aprovada: 

"O contêiner não está em um estado válido. Falha na validação da assinatura com o status ' incompatível '. A chave de API não se destina ao tipo de contêiner fornecido. "

Para obter mais informações, consulte [solicitar aprovação para executar o contêiner](spatial-analysis-container.md#request-approval-to-run-the-container).

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Solucionando problemas do dispositivo Azure Stack Edge

A seção a seguir é fornecida para obter ajuda com a depuração e a verificação do status do seu dispositivo Azure Stack Edge.

### <a name="access-the-kubernetes-api-endpoint"></a>Acesse o ponto de extremidade da API do kubernetes. 

1. Na interface do usuário local do seu dispositivo, vá para a página _ *dispositivos**. 
2. Em **pontos** de extremidade do dispositivo, copie o ponto de extremidades do serviço API do kubernetes. Esse ponto de extremidade é uma cadeia de caracteres no seguinte formato: `https://compute..[device-IP-address]`.
3. Salve a cadeia de caracteres do ponto de extremidade. Você o usará posteriormente ao configurar `kubectl` o para acessar o cluster kubernetes.

### <a name="connect-to-powershell-interface"></a>Conectar-se à interface do PowerShell

Remotamente, conecte-se de um cliente do Windows. Depois que o cluster kubernetes for criado, você poderá gerenciar os aplicativos por meio desse cluster. Você precisará se conectar à interface do PowerShell do dispositivo. Dependendo do sistema operacional do cliente, os procedimentos para se conectar remotamente ao dispositivo podem ser diferentes. As etapas a seguir são para um cliente Windows executando o PowerShell.

> [!TIP]
> * Antes de começar, verifique se o seu cliente Windows está executando o Windows PowerShell 5,0 ou posterior.
> * O PowerShell também está [disponível no Linux](/powershell/scripting/install/installing-powershell-core-on-linux).

1. Execute uma sessão do Windows PowerShell como administrador. 
    1. Verifique se o serviço de Gerenciamento Remoto do Windows está em execução no seu cliente. No prompt de comando, digite `winrm quickconfig`.

2. Atribua uma variável para o endereço IP do dispositivo. Por exemplo, `$ip = "<device-ip-address>"`.

3. Use o comando a seguir para adicionar o endereço IP do seu dispositivo à lista de hosts confiáveis do cliente. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Inicie uma sessão do Windows PowerShell no dispositivo. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. Forneça a senha quando solicitado. Use a mesma senha usada para entrar na interface da Web local. A senha padrão da interface da Web local é `Password1` . 

### <a name="access-the-kubernetes-cluster"></a>Acessar o cluster kubernetes

Depois que o cluster kubernetes for criado, você poderá usar a `kubectl` ferramenta de linha de comando para acessar o cluster.

1. Crie um novo namespace. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Crie um usuário e obtenha um arquivo de configuração. Esse comando produzirá informações de configuração para o cluster kubernetes. Copie essas informações e salve-as em um arquivo chamado *config*. Não salve o arquivo como uma extensão de arquivo.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. Adicione o arquivo de *configuração* à pasta *. Kube* em seu perfil de usuário no computador local.   

4. Associe o namespace ao usuário que você criou.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. Instale `kubectl` o no seu cliente Windows usando o seguinte comando:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Adicione uma entrada DNS ao arquivo de hosts em seu sistema. 
    1. Execute o bloco de notas como administrador e abra o arquivo de *hosts* localizado em `C:\windows\system32\drivers\etc\hosts` . 
    2. Crie uma entrada no arquivo de hosts com o endereço IP do dispositivo e o domínio DNS obtidos na página do **dispositivo** na interface do usuário local. O ponto de extremidade que deve ser usado será semelhante a: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` .

7. Verifique se você pode se conectar ao pods kubernetes.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

Para obter logs de contêiner, execute o seguinte comando:

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>Comandos úteis

|Comando  |Descrição  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Gera um arquivo de configuração kubernetes. Ao usar o comando, copie as informações em um arquivo chamado *config*. Não salve o arquivo com uma extensão de arquivo.        |
| `Get-HcsApplianceInfo` | Retorna informações sobre seu dispositivo. |
| `Enable-HcsSupportAccess` | Gera credenciais de acesso para iniciar uma sessão de suporte. |

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo Web de contagem de pessoas](spatial-analysis-web-app.md)
* [Configurar operações de análise espacial](./spatial-analysis-operations.md)
* [Guia de posicionamento da câmera](spatial-analysis-camera-placement.md)
* [Guia de posicionamento de zona e linha](spatial-analysis-zone-line-placement.md)
