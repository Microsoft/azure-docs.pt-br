---
title: Solução de problemas de Instâncias de Contêiner do Azure
description: Saiba como solucionar problemas com as Instâncias de Contêiner do Azure
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/15/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: bf783c988c0163fe562669a8331c332dbf8d535e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61067314"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Solucionar problemas comuns nas Instâncias de Contêiner do Azure

Este artigo mostra como solucionar problemas ao gerenciar ou implantar contêineres para Instâncias de Contêiner do Azure.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Ao definir a especificação de contêiner, alguns parâmetros exigem aderência às restrições de nomenclatura. Abaixo está uma tabela com requisitos específicos para o contêiner de propriedades do grupo. Para obter mais informações sobre convenções de nomenclatura do Azure, consulte [Convenções de nomenclatura][azure-name-restrictions] no Azure Architecture Center.

| Escopo | Comprimento | Capitalização | Caracteres válidos | Padrão sugerido | Exemplo |
| --- | --- | --- | --- | --- | --- |
| Nome do grupo de contêineres | 1-64 |Não diferencia maiúsculas de minúsculas |Alfanumérico e hífen em qualquer lugar, exceto o primeiro ou último caractere |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Nome do contêiner | 1-64 |Não diferencia maiúsculas de minúsculas |Alfanumérico e hífen em qualquer lugar, exceto o primeiro ou último caractere |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Portas de contêiner | Entre 1 e 65535 |Número inteiro |Um número inteiro entre 1 e 65535 |`<port-number>` |`443` |
| Rótulo do nome DNS | 5 a 63 |Não diferencia maiúsculas de minúsculas |Alfanumérico e hífen em qualquer lugar, exceto o primeiro ou último caractere |`<name>` |`frontend-site1` |
| Variável de ambiente | 1-63 |Não diferencia maiúsculas de minúsculas |Alfanumérico e sublinhado (_) em qualquer lugar, exceto o primeiro ou último caractere |`<name>` |`MY_VARIABLE` |
| Nome do volume | 5 a 63 |Não diferencia maiúsculas de minúsculas |Letras minúsculas, números e hifens em qualquer lugar, exceto o primeiro ou último caractere. Não pode conter dois hífenes consecutivos. |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>Não há suporte para a versão do SO da imagem

Se você especificar uma imagem sem suporte das Instâncias de Contêiner do Azure, um erro `OsVersionNotSupported` será retornado. O erro é semelhante ao seguinte, onde `{0}` é o nome da imagem que você tentou implantar:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Esse erro geralmente ocorre ao implantar imagens do Windows que baseiam-se em uma versão do SAC (Canal Semestral). Por exemplo, o as versões 1709 e 1803 do Windows são versões de SAC e geram esse erro após a implantação.

As Instâncias de Contêiner do Azure atualmente dão suporte a imagens do Windows com base somente na versão **LTSC (Canal de Atendimento de Longo Prazo) do Windows Server 2016**. Para atenuar esse problema ao implantar contêineres do Windows, sempre implante imagens baseadas em Windows Server 2016 (LTSC). Não há suporte para imagens baseadas no Windows Server 2019 (LTSC).

Para obter detalhes sobre as versões LTSC e SAC do Windows, consulte [Visão geral do Canal Semestral do Windows Server][windows-sac-overview].

## <a name="unable-to-pull-image"></a>Não é possível efetuar pull da imagem

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

## <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Contêiner sai e reinicia continuamente (sem processo de longa execução)

A [política de reinicialização](container-instances-restart-policy.md) padrão dos grupos de contêineres é **Sempre**; portanto, o grupo de contêineres sempre reiniciará após ser executado até a conclusão. Talvez seja necessário alterar isso para **OnFailure** ou **Nunca** se você pretende executar contêineres baseados em tarefa. Se você especificar **Em caso de Falha** e ainda continuar sendo reiniciado, pode haver um problema com o aplicativo ou script executado em seu contêiner.

Ao executar grupos de contêineres sem processos de longa execução, talvez você veja saídas e reinicializações repetidos com imagens como Ubuntu ou Alpine. Conectar-se por meio de [EXEC](container-instances-exec.md) não funcionará, porque o contêiner não tem nenhum processo que o mantenha ativo. Para resolver esse problema, inclua um comando de início, semelhante à seguinte com sua implantação do grupo de contêiner para manter o contêiner em execução.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2016
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

## <a name="container-takes-a-long-time-to-start"></a>Contêiner leva muito tempo para iniciar

Os dois principais fatores que contribuem para o tempo de inicialização do contêiner em Instâncias de Contêiner do Azure são:

* [Tamanho da imagem](#image-size)
* [Local da imagem](#image-location)

Imagens do Windows têm [considerações adicionais](#cached-windows-images).

### <a name="image-size"></a>Tamanho da imagem

Se o contêiner leva muito tempo para iniciar mas eventualmente tem êxito, comece observando o tamanho da sua imagem de contêiner. Como as Instâncias de Contêiner do Azure efetuam pull da imagem de contêiner sob demanda, o tempo de inicialização efetivo está diretamente relacionado ao tamanho delas.

Você pode exibir o tamanho da sua imagem de contêiner usando o comando `docker images` na CLI do Docker:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

A chave para manter os tamanhos de imagem pequenos é garantir que sua imagem final não contenha nada que não deja necessário no tempo de execução. Uma forma de fazer isso é com [builds de vários estágios][docker-multi-stage-builds]. Builds de vários estágios tornam fácil assegurar que a imagem final contenha somente os artefatos necessários para seu aplicativo, sem nenhum conteúdo extra que foi necessário no momento do build.

### <a name="image-location"></a>Local da imagem

Outra maneira de reduzir o impacto do pull da imagem no tempo de inicialização do contêiner é hospedar a imagem de contêiner no [Registro de Contêiner do Azure](/azure/container-registry/) na mesma região em que você pretende implantar as instâncias de contêiner. Isso reduz o caminho de rede que a imagem de contêiner precisa percorrer, reduzindo significativamente o tempo de download.

### <a name="cached-windows-images"></a>Imagens armazenadas em cache do Windows

As instâncias de contêiner do Azure usa um mecanismo de cache para ajudar a acelerar o tempo de inicialização de contêiner para imagens com base em imagens comuns do Windows e Linux. Para obter uma lista detalhada de marcas e imagens armazenadas em cache, use o [lista de imagens armazenadas em cache] [ list-cached-images] API.

Para garantir o menor tempo de inicialização do contêiner do Windows, use uma da **três versões mais recentes** das **duas imagens** seguintes como a imagem base:

* [Windows Server Core 2016] [ docker-hub-windows-core] (LTSC somente)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Preparação de rede lenta de contêineres do Windows

Na criação inicial, os contêineres do Windows podem não ter conectividade de entrada ou saída por até 30 segundos (ou mais, em casos raros). Se o aplicativo contêiner precisar de uma conexão com a Internet, adicione atraso e tente novamente a lógica para permitir que 30 segundos estabeleçam conectividade com a Internet. Após a configuração inicial, a rede de contêineres deverá de retomada apropriadamente.

## <a name="resource-not-available-error"></a>Erro de recurso não disponível

Devido a diversas cargas de recursos regionais no Azure, você poderá receber o seguinte erro durante a tentativa de implantar uma instância de contêiner:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Esse erro indica que devido à carga pesada na região em que você está tentando implantar, os recursos especificados para o contêiner não poderão ser alocados no momento. Use uma ou mais das seguintes etapas de mitigação para ajudar a resolver o problema.

* Verifique se suas configurações de implantação do contêiner caem dentro dos parâmetros definidos na [Disponibilidade de região para Instâncias de Contêiner do Azure](container-instances-region-availability.md)
* Especificar configurações de CPU e memória inferiores para o contêiner
* Implantar em uma região diferente do Azure
* Implantar em um momento posterior

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Não é possível conectar à API do Docker subjacente ou executar contêineres com privilégios

As Instâncias de Contêiner do Azure não expõem acesso direto para a infraestrutura subjacente que hospeda grupos de contêineres. Isso inclui o acesso à API do Docker em execução no host do contêiner e contêineres com privilégios em execução. Se exigir interação com o Docker, verifique a [Documentação de referência do REST](https://aka.ms/aci/rest) para ver o que é compatível com a API do ACI. Se faltar alguma coisa, envie uma solicitação nos [Fóruns de comentários do ACI](https://aka.ms/aci/feedback).

## <a name="ips-may-not-be-accessible-due-to-mismatched-ports"></a>IPs podem não estar acessíveis devido a portas incompatíveis

No momento, as Instâncias de Contêiner do Azure não suportam o mapeamento de portas como na configuração do Docker comum, mas essa correção está no roteiro. Se você achar que os IPs não estão acessíveis quando você acredita que devem estar, certifique-se de ter configurado sua imagem de contêiner para ouvir as mesmas portas expostas em seu grupo de contêineres com a propriedade `ports`.

## <a name="next-steps"></a>Próximas etapas

Saiba como [recuperar eventos e logs de contêiner](container-instances-get-logs.md) para ajudar a depurar seus contêineres.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
