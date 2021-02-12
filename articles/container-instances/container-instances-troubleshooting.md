---
title: Solução de problemas comuns
description: Saiba como solucionar problemas comuns ao implantar, executar ou gerenciar instâncias de contêiner do Azure
ms.topic: article
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d8e7fb85e369f5f278436370944eafeb1fb6a50e
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779508"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Solucionar problemas comuns nas Instâncias de Contêiner do Azure

Este artigo mostra como solucionar problemas ao gerenciar ou implantar contêineres para Instâncias de Contêiner do Azure. Consulte também [perguntas frequentes](container-instances-faq.md).

Se precisar de suporte adicional, consulte Opções de **ajuda + suporte** disponíveis no [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="issues-during-container-group-deployment"></a>Problemas durante a implantação do grupo de contêineres
### <a name="naming-conventions"></a>Convenções de nomenclatura

Ao definir a especificação de contêiner, alguns parâmetros exigem aderência às restrições de nomenclatura. Abaixo está uma tabela com requisitos específicos para o contêiner de propriedades do grupo. Para obter mais informações, consulte [convenções de nomenclatura][azure-name-restrictions] no centro de arquitetura do Azure e [regras de nomenclatura e restrições para recursos do Azure][naming-rules].

| Escopo | Comprimento | Capitalização | Caracteres válidos | Padrão sugerido | Exemplo |
| --- | --- | --- | --- | --- | --- |
| Nome do contêiner<sup>1</sup> | 1-63 |Minúsculas | Alfanumérico e hífen em qualquer lugar, exceto o primeiro ou último caractere |`<name>-<role>-container<number>` |`web-batch-container1` |
| Portas de contêiner | Entre 1 e 65535 |Integer |Um número inteiro entre 1 e 65535 |`<port-number>` |`443` |
| Rótulo do nome DNS | 5 a 63 |Não diferencia maiúsculas de minúsculas |Alfanumérico e hífen em qualquer lugar, exceto o primeiro ou último caractere |`<name>` |`frontend-site1` |
| Variável de ambiente | 1-63 |Não diferencia maiúsculas de minúsculas |Alfanumérico e sublinhado (_) em qualquer lugar, exceto o primeiro ou último caractere |`<name>` |`MY_VARIABLE` |
| Nome do volume | 5 a 63 |Minúsculas |Alfanuméricos e hifens em qualquer lugar, exceto o primeiro ou o último caractere. Não pode conter dois hífenes consecutivos. |`<name>` |`batch-output-volume` |

<sup>1</sup> Restrição também para nomes de grupos de contêineres quando não especificados independentemente de instâncias de contêiner, por exemplo, com `az container create` implantações de comando.

### <a name="os-version-of-image-not-supported"></a>Não há suporte para a versão do SO da imagem

Se você especificar uma imagem sem suporte das Instâncias de Contêiner do Azure, um erro `OsVersionNotSupported` será retornado. O erro é semelhante ao seguinte, onde `{0}` é o nome da imagem que você tentou implantar:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Esse erro é encontrado com mais frequência ao implantar imagens do Windows baseadas em Semi-Annual versão do canal 1709 ou 1803, que não tem suporte. Para imagens do Windows com suporte em instâncias de contêiner do Azure, consulte [perguntas](container-instances-faq.md#what-windows-base-os-images-are-supported)frequentes.

### <a name="unable-to-pull-image"></a>Não é possível efetuar pull da imagem

Se as Instâncias de Contêiner do Azure não puderem efetuar pull da imagem inicialmente, elas tentarão novamente durante um período de tempo. Se a operação de pull de imagem continuar a falhar, o ACI eventualmente falhará na implantação e um erro `Failed to pull image` será exibido.

Para resolver esse problema, exclua a instância de contêiner e tente a implantação novamente. Certifique-se de que a imagem existe no registro e que você digitou corretamente o nome da imagem.

Se não for possível efetuar pull da imagem, eventos como a seguir serão mostrados na saída do [az container show][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```
### <a name="resource-not-available-error"></a>Erro de recurso não disponível

Devido a diversas cargas de recursos regionais no Azure, você poderá receber o seguinte erro durante a tentativa de implantar uma instância de contêiner:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Esse erro indica que devido à carga pesada na região em que você está tentando implantar, os recursos especificados para o contêiner não poderão ser alocados no momento. Use uma ou mais das seguintes etapas de mitigação para ajudar a resolver o problema.

* Verifique se suas configurações de implantação do contêiner caem dentro dos parâmetros definidos na [Disponibilidade de região para Instâncias de Contêiner do Azure](container-instances-region-availability.md)
* Especificar configurações de CPU e memória inferiores para o contêiner
* Implantar em uma região diferente do Azure
* Implantar em um momento posterior

## <a name="issues-during-container-group-runtime"></a>Problemas durante o tempo de execução do grupo de contêineres
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Contêiner sai e reinicia continuamente (sem processo de longa execução)

A [política de reinicialização](container-instances-restart-policy.md) padrão dos grupos de contêineres é **Sempre**; portanto, o grupo de contêineres sempre reiniciará após ser executado até a conclusão. Talvez seja necessário alterar isso para **OnFailure** ou **Nunca** se você pretende executar contêineres baseados em tarefa. Se você especificar **Em caso de Falha** e ainda continuar sendo reiniciado, pode haver um problema com o aplicativo ou script executado em seu contêiner.

Ao executar grupos de contêineres sem processos de longa execução, talvez você veja saídas e reinicializações repetidos com imagens como Ubuntu ou Alpine. Conectar-se por meio de [EXEC](container-instances-exec.md) não funcionará, porque o contêiner não tem nenhum processo que o mantenha ativo. Para resolver esse problema, inclua um comando Iniciar, como o seguinte, com a implantação do grupo de contêineres para manter o contêiner em execução.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

A API de Instâncias de Contêiner e o portal do Azure incluem uma propriedade `restartCount`. Para verificar o número de reinícios de um contêiner, é possível usar o comando [az container show][az-container-show] na CLI do Azure. No seguinte exemplo de saída (que foi truncado para fins de brevidade), é possível ver a propriedade `restartCount` no final da saída.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> A maioria das imagens de contêiner para as distribuições de Linux definem um shell (assim como um bash) como o comando padrão. Já que um shell por si só não é um serviço de execução longa, esses contêineres saem imediatamente e entram em um loop de reinicialização quando configurados com a política de reinício padrão **Sempre**.

### <a name="container-takes-a-long-time-to-start"></a>Contêiner leva muito tempo para iniciar

Os três fatores principais que contribuem para o tempo de inicialização do contêiner em instâncias de contêiner do Azure são:

* [Tamanho da imagem](#image-size)
* [Local da imagem](#image-location)
* [Imagens armazenadas em cache](#cached-images)

Imagens do Windows têm [considerações adicionais](#cached-images).

#### <a name="image-size"></a>Tamanho da imagem

Se o contêiner leva muito tempo para iniciar mas eventualmente tem êxito, comece observando o tamanho da sua imagem de contêiner. Como as Instâncias de Contêiner do Azure efetuam pull da imagem de contêiner sob demanda, o tempo de inicialização efetivo está diretamente relacionado ao tamanho delas.

Você pode exibir o tamanho da sua imagem de contêiner usando o comando `docker images` na CLI do Docker:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

A chave para manter os tamanhos de imagem pequenos é garantir que sua imagem final não contenha nada que não deja necessário no runtime. Uma forma de fazer isso é com [builds de vários estágios][docker-multi-stage-builds]. Builds de vários estágios tornam fácil assegurar que a imagem final contenha somente os artefatos necessários para seu aplicativo, sem nenhum conteúdo extra que foi necessário no momento do build.

#### <a name="image-location"></a>Local da imagem

Outra maneira de reduzir o impacto do pull da imagem no tempo de inicialização do contêiner é hospedar a imagem de contêiner no [Registro de Contêiner do Azure](../container-registry/index.yml) na mesma região em que você pretende implantar as instâncias de contêiner. Isso reduz o caminho de rede que a imagem de contêiner precisa percorrer, reduzindo significativamente o tempo de download.

#### <a name="cached-images"></a>Imagens armazenadas em cache

As instâncias de contêiner do Azure usam um mecanismo de cache para ajudar a acelerar o tempo de inicialização do contêiner para imagens criadas em [imagens básicas](container-instances-faq.md#what-windows-base-os-images-are-supported)comuns do Windows, incluindo `nanoserver:1809` , `servercore:ltsc2019` e `servercore:1809` . Imagens do Linux comumente usadas, como `ubuntu:1604` e `alpine:3.6` também são armazenadas em cache. Para imagens do Windows e do Linux, evite usar a `latest` marca. Examine [as práticas recomendadas da marca de imagem](../container-registry/container-registry-image-tag-version.md) do registro de contêiner para obter diretrizes. Para obter uma lista atualizada de imagens e marcas armazenadas em cache, use a API da [lista de imagens em cache][list-cached-images] .

> [!NOTE]
> Use as imagens com base no Windows Server 2019 nas instâncias de contêiner do Azure nesta versão prévia.

#### <a name="windows-containers-slow-network-readiness"></a>Preparação de rede lenta de contêineres do Windows

Na criação inicial, os contêineres do Windows podem não ter conectividade de entrada ou saída por até 30 segundos (ou mais, em casos raros). Se o aplicativo contêiner precisar de uma conexão com a Internet, adicione atraso e tente novamente a lógica para permitir que 30 segundos estabeleçam conectividade com a Internet. Após a configuração inicial, a rede de contêineres deverá de retomada apropriadamente.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Não é possível conectar à API do Docker subjacente ou executar contêineres com privilégios

As Instâncias de Contêiner do Azure não expõem acesso direto para a infraestrutura subjacente que hospeda grupos de contêineres. Isso inclui o acesso à API do Docker em execução no host do contêiner e contêineres com privilégios em execução. Se exigir interação com o Docker, verifique a [Documentação de referência do REST](/rest/api/container-instances/) para ver o que é compatível com a API do ACI. Se faltar alguma coisa, envie uma solicitação nos [Fóruns de comentários do ACI](https://aka.ms/aci/feedback).

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>O endereço IP do grupo de contêineres pode não estar acessível devido a portas incompatíveis

As instâncias de contêiner do Azure ainda não dão suporte ao mapeamento de porta como com a configuração regular do Docker. Se você achar que o endereço IP de um grupo de contêineres não está acessível quando acreditar que deveria ser, certifique-se de ter configurado sua imagem de contêiner para escutar as mesmas portas que você expõe em seu grupo de contêineres com a `ports` propriedade.

Se você quiser confirmar que as instâncias de contêiner do Azure podem escutar na porta configurada na sua imagem de contêiner, teste uma implantação da `aci-helloworld` imagem que expõe a porta. Execute também o `aci-helloworld` aplicativo para que ele escute na porta. `aci-helloworld` aceita uma variável de ambiente opcional `PORT` para substituir a porta padrão 80 escutada. Por exemplo, para testar a porta 9000, defina a [variável de ambiente](container-instances-environment-variables.md) ao criar o grupo de contêineres:

1. Configure o grupo de contêineres para expor a porta 9000 e passe o número da porta como o valor da variável de ambiente. O exemplo é formatado para o shell bash. Se preferir outro shell, como o PowerShell ou o prompt de comando, você precisará ajustar a atribuição de variável de forma adequada.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Localize o endereço IP do grupo de contêineres na saída de comando de `az container create` . Procure o valor de **IP**. 
1. Depois que o contêiner for provisionado com êxito, navegue até o endereço IP e a porta do aplicativo de contêiner em seu navegador, por exemplo: `192.0.2.0:9000` . 

    Você deve ver o "bem-vindo às instâncias de contêiner do Azure!" mensagem exibida pelo aplicativo Web.
1. Quando você terminar o contêiner, remova-o usando o `az container delete` comando:

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Próximas etapas

Saiba como [recuperar logs de contêiner e eventos](container-instances-get-logs.md) para ajudar a depurar seus contêineres.

<!-- LINKS - External -->
[azure-name-restrictions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[naming-rules]: ../azure-resource-manager/management/resource-name-rules.md
[windows-sac-overview]: /windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/location/listcachedimages
