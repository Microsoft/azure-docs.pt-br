---
title: Telemetria e registro em log para contêineres de Análise Espacial
titleSuffix: Azure Cognitive Services
description: A Análise Espacial fornece a cada contêiner uma configuração comum de insights de estrutura, de registro em log e de segurança.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 901e857a346b0955726c5755e23595efefbc2ca1
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589492"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetria e solução de problemas

A Análise Espacial inclui um conjunto de recursos para monitorar a integridade do sistema e ajudar a diagnosticar problemas.

## <a name="enable-visualizations"></a>Habilitar visualizações

Para habilitar uma visualização dos eventos de Insights da IA em um quadro de vídeo, você precisa usar a versão `.debug` de uma [operação de Análise Espacial](spatial-analysis-operations.md) em um computador desktop. A visualização não é possível em dispositivos do Azure Stack Edge. Há quatro operações de depuração disponíveis.

Se o seu dispositivo não for do Azure Stack Edge, edite o arquivo de manifesto de implantação para [computadores desktop](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json), a fim de usar o valor correto para a variável de ambiente `DISPLAY`. Ele precisa corresponder à variável `$DISPLAY` no computador host. Depois de atualizar o manifesto de implantação, reimplante o contêiner.

Após a conclusão da implantação, talvez seja necessário copiar o arquivo `.Xauthority` do computador host para o contêiner e reiniciá-lo. No exemplo abaixo, `peopleanalytics` é o nome do contêiner no computador host.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Coletar telemetria de integridade do sistema

O Telegraf é uma imagem de software livre que funciona com Análise Espacial e está disponível no Microsoft Container Registry. Ele usa as entradas a seguir e as envia para o Azure Monitor. O módulo Telegraf pode ser compilado com saídas e entradas personalizadas desejadas. A configuração do módulo Telegraf na Análise Espacial faz parte do manifesto de implantação (vinculado acima). Esse módulo é opcional e pode ser removido do manifesto se você não precisar dele. 

Entradas: 
1. Métricas de Análise Espacial
2. Métricas de disco
3. Métricas de CPU
4. Métricas do Docker
5. Métricas de GPU

Saídas:
1. Azure Monitor

O módulo Telegraf de Análise Espacial fornecido publicará todos os dados de telemetria emitidos pelo contêiner de Análise Espacial para o Azure Monitor. Confira o [Azure Monitor](../../azure-monitor/overview.md) para obter informações sobre como adicionar o Azure Monitor à sua assinatura.

Após a configuração do Azure Monitor, você precisará criar credenciais que habilitem o módulo para o envio de telemetria. Você pode usar o portal do Azure para criar uma Entidade de Serviço ou usar o comando da CLI do Azure abaixo para criar uma.

> [!NOTE] 
> Esse comando exige que você tenha privilégios de Proprietário na assinatura. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/..."
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

No manifesto de implantação do [dispositivo do Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), do [computador desktop](https://go.microsoft.com/fwlink/?linkid=2152270) ou da [VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189), procure o módulo *Telegraf*, substitua os valores a seguir pelas informações da Entidade de Serviço da etapa anterior e faça a reimplantação.

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

Depois que o módulo Telegraf é implantado, as métricas relatadas podem ser acessadas por meio do serviço Azure Monitor ou selecionando **Monitoramento** no Hub IoT no portal do Azure.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Relatório de telemetria do Azure Monitor":::

### <a name="system-health-events"></a>Eventos de integridade do sistema

| Nome do evento                  | Descrição    |
|-----------------------------|-------------------------------------------------------------------------------------------|
| archon_exit                 | Enviado quando um usuário altera o status do módulo de Análise Espacial de *em execução* para *interrompido*.  |
| archon_error                | Enviado quando ocorre uma falha em qualquer um dos processos dentro do contêiner. Esse é um evento crítico.      |
| InputRate                   | A taxa na qual o grafo processa a entrada de vídeo. Relatado a cada cinco minutos.              |
| OutputRate                  | A taxa na qual o grafo gera insights de IA. Relatado a cada cinco minutos.                |
| archon_allGraphsStarted     | Enviado quando todos os gráficos concluem a inicialização.                                           |
| archon_configchange         | Enviado quando uma configuração de grafo é alterada.                                              |
| archon_graphCreationFailed  | Enviado quando o grafo com o `graphId` relatado não é iniciado.                           |
| archon_graphCreationSuccess | Enviado quando o grafo com o `graphId` relatado é iniciado com sucesso.                      |
| archon_graphCleanup         | Enviado quando o grafo com o `graphId` relatado é limpo e encerrado.                      |
| archon_graphHeartbeat       | Pulsação enviada a cada minuto para cada grafo de uma habilidade.                                   |
| archon_apiKeyAuthFail       | Enviado quando a chave de recurso de Pesquisa Visual Computacional não consegue autenticar o contêiner por mais de 24 horas, devido aos seguintes motivos: Sem Cota, Inválido, Offline. |
| VideoIngesterHeartbeat      | Enviado a cada hora para indicar que o vídeo é transmitido da fonte de vídeo, com o número de erros naquela hora. Relatado para cada grafo. |
| VideoIngesterState          | Relata *Interrompido* ou *Iniciado* para streaming de vídeo. Relatado para cada grafo.              |

##  <a name="troubleshooting-an-iot-edge-device"></a>Solução de problemas de um Dispositivo de IoT Edge

Você pode usar a ferramenta de linha de comando `iotedge` para verificar o status e os logs dos módulos em execução. Por exemplo:
* `iotedge list`: relata uma lista de módulos em execução. 
  Você pode verificar se há erros com o `iotedge logs edgeAgent`. Se o `iotedge` travar, você pode tentar reiniciá-lo com `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` para reiniciar um módulo específico 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Coletar arquivos de log com o contêiner de diagnóstico

A Análise Espacial gera logs de depuração do Docker que você pode usar para diagnosticar problemas de runtime ou incluir tíquetes de suporte. O módulo de diagnóstico de Análise Espacial está disponível no Microsoft Container Registry para download. No arquivo de implantação de manifesto para o [Dispositivo do Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), o [computador desktop](https://go.microsoft.com/fwlink/?linkid=2152270) ou o [VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189), procure o módulo de *diagnóstico*.

Na seção "env", adicione a seguinte configuração:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Para otimizar os logs carregados em um ponto de extremidade remoto, como o Armazenamento de Blobs do Azure, recomendamos que você mantenha um tamanho de arquivo pequeno. Confira o exemplo abaixo para obter a configuração de logs do Docker recomendada.

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

### <a name="configure-the-log-level"></a>Configurar o nível do log

A configuração do nível de log permite que você controle o detalhamento dos logs gerados. Os níveis de log compatíveis são: `none`, `verbose`, `info`, `warning` e `error`. O nível detalhado de log padrão para os nós e a plataforma é `info`. 

Os níveis de log podem ser modificados globalmente definindo a variável de ambiente `ARCHON_LOG_LEVEL` para um dos valores permitidos.
Ele também pode ser definido por meio do documento do módulo gêmeo do IoT Edge, seja globalmente, para todas as habilidades implantadas ou para cada habilidade específica, definindo os valores de `platformLogLevel` e `nodesLogLevel` conforme mostrado abaixo.

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
                    "nodesLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>coletando logs

> [!NOTE]
> O módulo `diagnostics` não afeta o registro em log, ele só ajuda a coletar, filtrar e carregar os logs existentes.
> Você deve ter a versão 1.40 ou superior da API do Docker para usar esse módulo.

O arquivo de manifesto de implantação de exemplo para o [dispositivo do Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), o [computador desktop](https://go.microsoft.com/fwlink/?linkid=2152270) ou a [VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189) inclui um módulo chamado `diagnostics` que coleta e carrega logs. Esse módulo é desabilitado por padrão e deve ser habilitado por meio da configuração do módulo do IoT Edge quando você precisa acessar os logs. 

A coleção `diagnostics` é sob demanda e controlada por meio de um método direto do IoT Edge e pode enviar logs para um Armazenamento de Blobs do Azure.

### <a name="configure-diagnostics-upload-targets"></a>Configurar destinos de upload de diagnóstico

No portal do IoT Edge, selecione o dispositivo e, depois, o módulo **diagnóstico**. No arquivo de manifesto de implantação de exemplo para o [dispositivo do Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), os [computadores desktop](https://go.microsoft.com/fwlink/?linkid=2152270) ou a [VM do Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189), procure a seção **Variáveis de Ambiente** para diagnóstico, chamado `env`, e adicione as seguintes informações:

**Configurar upload para o Armazenamento de Blobs do Azure**

1. Crie a própria conta de Armazenamento de Blobs do Azure, caso ainda não tenha feito isso.
2. Obtenha a **Cadeia de Conexão** da sua conta de armazenamento por meio do portal do Azure. Ela estará localizada em **Chaves de Acesso**.
3. Os logs de Análise Espacial serão automaticamente carregados em um contêiner de Armazenamento de Blobs chamado *rtcvlogs* com o seguinte formato de nome de arquivo: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log`.

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Como carregar logs de Análise Espacial

Os logs são carregados sob demanda com o método `getRTCVLogs` do IoT Edge, no módulo `diagnostics`. 


1. Acesse a página do portal do Hub IoT, selecione **Dispositivos de Borda** e escolha o dispositivo e o módulo de diagnóstico. 
2. Acesse a página de detalhes do módulo e clique na guia ***método direto***.
3. Digite `getRTCVLogs` em Nome do Método e uma cadeia de caracteres de formato JSON no conteúdo. Você pode inserir `{}`, que é um conteúdo vazio. 
4. Defina a conexão e os tempos limite do método e clique em **Invocar Método**.
5. Selecione o contêiner de destino e crie uma cadeia de caracteres JSON de conteúdo usando os parâmetros descritos na seção de **Sintaxe de registro em log**. Clique em **Invocar Método** para executar a solicitação.

>[!NOTE]
> A invocação do método `getRTCVLogs` com um conteúdo vazio retornará uma lista de todos os contêineres implantados no dispositivo. O nome do método diferencia maiúsculas de minúsculas. Você receberá um erro 501 se um nome de método incorreto for fornecido.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="Como invocar o método getRTCVLogs ":::
![página de método direto do getRTCVLogs](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Sintaxe de registro em log

A tabela abaixo lista os parâmetros que você pode usar ao consultar logs.

| Palavra-chave | Descrição | Valor padrão |
|--|--|--|
| StartTime | Hora de início dos logs desejados, UTC em milissegundos. | `-1`, o início do runtime do contêiner. Quando `[-1.-1]` é usado como um intervalo de tempo, a API retorna logs da última hora.|
| EndTime | Hora de término dos logs desejados, UTC em milissegundos. | `-1`, a hora atual. Quando o intervalo de tempo `[-1.-1]` é usado, a API retorna logs da última hora. |
| ContainerId | Contêiner de destino para a busca de logs.| `null`, quando não há nenhuma ID de contêiner. A API retorna todas as informações de contêineres disponíveis com IDs.|
| DoPost | Realiza a operação de atualização. Ao ser definido como `false`, ele executa a operação solicitada e retorna o tamanho do carregamento sem executá-lo. Ao ser definido como `true`, ele iniciará o carregamento assíncrono dos logs selecionados | `false`, não faça o upload.|
| Restrição | Indica quantas linhas de logs devem ser carregadas por lote | `1000`, Use esse parâmetro para ajustar a velocidade da postagem. |
| Filtros | Filtra os logs a serem carregados | `null`, os filtros podem ser especificados como pares de chave-valor com base na estrutura de registros da Análise Espacial: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]`. Por exemplo: `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

A tabela a seguir lista os atributos na resposta da consulta.

| Palavra-chave | Descrição|
|--|--|
|DoPost| *Verdadeiro* ou *falso*. Indica se os logs foram carregados ou não. Quando você opta por não fazer upload dos logs, a API retorna informações ***de modo síncrono** _. Quando você optar por carregar os logs, a API retornará 200 se a solicitação for válida e começará a carregar logs _*_de modo síncrono_**.|
|TimeFilter| Filtro de tempo aplicado aos logs.|
|ValueFilters| Filtros de palavras-chave aplicados aos registros. |
|TimeStamp| Hora de início da execução do método. |
|ContainerId| ID do contêiner de destino. |
|FetchCounter| Número total de linhas de log. |
|FetchSizeInByte| Quantidade total de dados de log em bytes. |
|MatchCounter| Número válido de linhas de log. |
|MatchSizeInByte| Quantidade válida de dados de log em bytes. |
|FilterCount| Número total de linhas de log após a aplicação do filtro. |
|FilterSizeInByte| Número total de linhas de log em bytes após a aplicação do filtro. |
|FetchLogsDurationInMiliSec| Busca a duração da operação. |
|PaseLogsDurationInMiliSec| Filtra a duração da operação. |
|PostLogsDurationInMiliSec| Posta a duração da operação. |

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

Verifique as linhas, os horários e tamanhos do log de busca. Se essas configurações forem boas, substitua ***DoPost*** por `true` e isso enviará os logs com os mesmos filtros para os destinos. 

Você pode exportar os logs do Armazenamento de Blobs do Azure ao solucionar problemas. 

## <a name="common-issues"></a>Problemas comuns

Se você vir a seguinte mensagem nos logs de módulo, isso poderá significar que a assinatura do Azure precisa ser aprovada: 

"O contêiner não está em um estado válido. Falha na validação da assinatura com o status 'Incompatível'. A chave da API não se destina ao tipo de contêiner fornecido."

Para obter mais informações, confira [Solicitar aprovação para executar o contêiner](spatial-analysis-container.md#request-approval-to-run-the-container).

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Solução de problemas do dispositivo do Azure Stack Edge

A seção a seguir é fornecida para obter ajuda com a depuração e a verificação do status do dispositivo do Azure Stack Edge.

### <a name="access-the-kubernetes-api-endpoint"></a>Acessar o Ponto de Extremidade de API do Kubernetes. 

1. Na IU local do dispositivo, acesse a página **Dispositivos**. 
2. Nos **Pontos de extremidade do dispositivo**, copie o ponto de extremidade de serviço da API do Kubernetes. Esse ponto de extremidade é uma cadeia de caracteres no seguinte formato: `https://compute..[device-IP-address]`.
3. Salve a cadeia de caracteres do ponto de extremidade. Você a usará posteriormente ao configurar `kubectl` para acesso ao cluster do Kubernetes.

### <a name="connect-to-powershell-interface"></a>Conectar-se à interface do PowerShell

Remotamente, conecte-se usando um cliente do Windows. Depois que o cluster do Kubernetes for criado, você poderá gerenciar os aplicativos por meio desse cluster. Você precisará se conectar à interface do PowerShell do dispositivo. Dependendo do sistema operacional do cliente, os procedimentos para se conectar remotamente ao dispositivo podem ser diferentes. As etapas a seguir são para um cliente do Windows executando o PowerShell.

> [!TIP]
> * Antes de começar, verifique se o cliente do Windows está executando o Windows PowerShell 5.0 ou posterior.
> * O PowerShell também está [disponível no Linux](/powershell/scripting/install/installing-powershell-core-on-linux).

1. Execute uma sessão do Windows PowerShell como Administrador. 
    1. Verifique se o serviço de Gerenciamento Remoto do Windows está em execução no cliente. No prompt de comando, digite `winrm quickconfig`.

2. Atribua uma variável ao endereço IP do dispositivo. Por exemplo, `$ip = "<device-ip-address>"`.

3. Use o comando a seguir para adicionar o endereço IP do dispositivo à lista de hosts confiáveis do cliente. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Inicie uma sessão do Windows PowerShell no dispositivo. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. Forneça a senha quando solicitada. Use a mesma senha usada para entrar na interface da Web local. A senha padrão da interface da Web local é `Password1`. 

### <a name="access-the-kubernetes-cluster"></a>Acesse o cluster do Kubernetes

Depois que o cluster do Kubernetes é criado, você pode usar a ferramenta de linha de comando `kubectl` para acessar o cluster.

1. Crie um namespace. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Crie um usuário e obtenha um arquivo de configuração. Esse comando produzirá informações de configuração para o cluster do Kubernetes. Copie essas informações e salve-as em um arquivo chamado *config*. Não o salve como uma extensão de arquivo.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. Adicione o arquivo *config* à pasta *.kube* no perfil do usuário no computador local.    

4. Associe o namespace ao usuário que você criou.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. Instale `kubectl` no seu cliente do Windows usando o seguinte comando:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Adicione uma entrada DNS ao arquivo de hosts no sistema. 
    1. Execute o Bloco de notas como administrador e abra o arquivo *hosts* localizado em `C:\windows\system32\drivers\etc\hosts`. 
    2. Crie uma entrada no arquivo de hosts com o endereço IP do dispositivo e o domínio DNS obtidos na página do **Dispositivo** na IU local. O ponto de extremidade que deve ser usado será semelhante a: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10`.

7. Verifique se você pode se conectar aos pods do Kubernetes.

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
|`Get-HcsKubernetesUserConfig -AseUser`     | Gera um arquivo de configuração do Kubernetes. Ao usar o comando, copie as informações em um arquivo chamado *config*. Não o salve com uma extensão de arquivo.        |
| `Get-HcsApplianceInfo` | Retorna informações sobre o dispositivo. |
| `Enable-HcsSupportAccess` | Gera credenciais de acesso para iniciar uma sessão de suporte. |


## <a name="how-to-file-a-support-ticket-for-spatial-analysis"></a>Como arquivar um tíquete de suporte para Análise Espacial 

Se precisar de mais suporte para encontrar uma solução para um problema que está tendo com o contêiner de Análise Espacial, siga as etapas abaixo para preencher e enviar um tíquete de suporte. Nossa equipe entrará em contato com você com diretrizes adicionais. 

### <a name="fill-out-the-basics"></a>Preencha as informações básicas 
Crie um tíquete de suporte na página [Nova solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Siga os prompts para preencher os seguintes parâmetros:

![Informações básicas de suporte](./media/support-ticket-page-1-final.png)

1. Defina o **Tipo de Problema** como `Technical`.
2. Selecione a assinatura que você está utilizando para implantar o contêiner de Análise Espacial.
3. Selecione `My services` e selecione `Cognitive Services` como o serviço.
4. Selecione o recurso que você está utilizando para implantar o contêiner de Análise Espacial.
5. Escreva uma breve descrição detalhando o problema que você está enfrentando. 
6. Selecione `Spatial Analysis` como o tipo de problema.
7. Selecione o subtipo apropriado no menu suspenso.
8. Selecione **Avançar: Soluções** e avance para a próxima página.

### <a name="recommended-solutions"></a>Soluções recomendadas
A próxima fase oferecerá as soluções recomendadas para o tipo de problema que você selecionou. Essas soluções resolverão os problemas mais comuns, mas se elas não forem úteis para sua solução, selecione **Avançar: Detalhes** e avance para a próxima etapa.

### <a name="details"></a>Detalhes
Nesta página, adicione algumas informações adicionais sobre o problema que você está enfrentando. Lembre-se de incluir o máximo de detalhes possível, pois isso ajudará nossos engenheiros a restringir melhor o problema. Inclua seu método de contato preferencial e a severidade do problema para que possamos entrar em contato com você, selecione **Avançar: Examinar + Criar** e avance para a próxima etapa. 

### <a name="review-and-create"></a>Examinar e criar 
Examine os detalhes da solicitação de suporte para garantir que tudo seja preciso e represente o problema com eficiência. Quando estiver pronto, selecione **Criar** para enviar o tíquete para a nossa equipe! Você receberá uma confirmação por email quando seu tíquete for recebido e nossa equipe trabalhará para entrar em contato com você o mais rápido possível. Você pode ver o status do tíquete no portal do Azure.

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo Web de contagem de pessoas](spatial-analysis-web-app.md)
* [Configurar operações de análise espacial](./spatial-analysis-operations.md)
* [Guia de posicionamento da câmera](spatial-analysis-camera-placement.md)
* [Guia de posicionamento de zona e de linha](spatial-analysis-zone-line-placement.md)
