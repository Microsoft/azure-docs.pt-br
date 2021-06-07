---
title: Investigações de Service Fabric do Azure
description: Como modelar uma investigação de vida no Azure Service Fabric usando arquivos de manifesto de aplicativo e serviço.
ms.topic: conceptual
author: tugup
ms.author: tugup
ms.date: 3/12/2020
ms.openlocfilehash: 07a1b836ca7ea79244e303f54654dfcaa6e5fcb9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "82137579"
---
# <a name="liveness-probe"></a>Investigação de tempo de vida
A partir da versão 7,1, o Azure Service Fabric dá suporte a um mecanismo de teste de vida para aplicativos em [contêineres][containers-introduction-link] . Uma investigação de tempo de vida ajuda a relatar a vida de um aplicativo em contêineres, que será reiniciado se ele não responder rapidamente.
Este artigo fornece uma visão geral de como definir uma investigação de vida usando arquivos de manifesto.

Antes de prosseguir com este artigo, familiarize-se com o [modelo de aplicativo Service Fabric][application-model-link] e o [modelo de Hospedagem de Service Fabric][hosting-model-link].

> [!NOTE]
> A investigação de tempo só tem suporte para contêineres no modo de rede NAT.

## <a name="semantics"></a>Semântica
Você pode especificar apenas uma investigação de vida por contêiner e pode controlar seu comportamento usando estes campos:

* `initialDelaySeconds`: O atraso inicial em segundos para iniciar a execução da investigação após o contêiner ter sido iniciado. O valor com suporte é **int**. O padrão é 0 e o mínimo é 0.

* `timeoutSeconds`: O período em segundos após o qual consideramos a investigação como com falha, se ela não tiver sido concluída com êxito. O valor com suporte é **int**. O padrão é 1 e o mínimo é 1.

* `periodSeconds`: O período em segundos para especificar a frequência da investigação. O valor com suporte é **int**. O padrão é 10 e o mínimo é 1.

* `failureThreshold`: Quando atingirmos esse valor, o contêiner será reiniciado. O valor com suporte é **int**. O padrão é 3 e o mínimo é 1.

* `successThreshold`: Em caso de falha, para que a investigação seja considerada com êxito, ela precisa ser executada com êxito para esse valor. O valor com suporte é **int**. O padrão é 1 e o mínimo é 1.

Pode haver, no máximo, uma investigação para um contêiner a qualquer momento. Se a investigação não for concluída no tempo definido em **timeoutSeconds**, aguarde e conte o tempo para o **limite**. 

Além disso, Service Fabric gerará os seguintes [relatórios de integridade][health-introduction-link] de investigação em **DeployedServicePackage**:

* `OK`: A investigação é realizada com sucesso para o valor definido em **successThreshold**.

* `Error`: A investigação **failureCount**  ==   **limite**, antes da reinicialização do contêiner.

* `Warning`: 
    * A investigação falha e **failureCount**  <  **limite**. Esse relatório de integridade permanece até que **failureCount** atinja o valor definido em **limite** ou **successThreshold**.
    * Em caso de êxito após a falha, o aviso permanece, mas com sucessos consecutivos atualizados.

## <a name="specifying-a-liveness-probe"></a>Especificando uma investigação de tempo de vida

Você pode especificar uma investigação no arquivo de ApplicationManifest.xml em **ServiceManifestImport**.

A investigação pode ser para qualquer um dos seguintes:

* HTTP
* TCP
* Exec 

### <a name="http-probe"></a>Investigação HTTP

Para uma investigação HTTP, Service Fabric enviará uma solicitação HTTP para a porta e o caminho que você especificar. Um código de retorno maior ou igual a 200 e menor que 400, indica êxito.

Aqui está um exemplo de como especificar uma investigação HTTP:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <HttpGet Path="/" Port="8081" Scheme="http">
              <HttpHeader Name="Foo" Value="Val"/>
              <HttpHeader Name="Bar" Value="val1"/>
            </HttpGet>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

A investigação HTTP tem propriedades adicionais que podem ser definidas:

* `path`: O caminho a ser usado na solicitação HTTP.

* `port`: A porta a ser usada para investigações. Essa propriedade é obrigatória. O intervalo é de 1 a 65535.

* `scheme`: O esquema a ser usado para se conectar ao pacote de códigos. Se essa propriedade for definida como HTTPS, a verificação do certificado será ignorada. A configuração padrão é HTTP.

* `httpHeader`: Os cabeçalhos a serem definidos na solicitação. Você pode especificar vários cabeçalhos.

* `host`: O endereço IP do host ao qual se conectar.

### <a name="tcp-probe"></a>Investigação TCP

Para uma investigação TCP, Service Fabric tentará abrir um soquete no contêiner usando a porta especificada. Se ele puder estabelecer uma conexão, a investigação será considerada com êxito. Aqui está um exemplo de como especificar uma investigação que usa um soquete TCP:

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <TcpSocket Port="8081"/>
          </Probe>
        </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

### <a name="exec-probe"></a>Investigação de exec

Essa investigação emitirá um comando **exec** no contêiner e aguardará a conclusão do comando.

> [!NOTE]
> O comando **exec** usa uma cadeia de caracteres separada por vírgula. O comando no exemplo a seguir funcionará para um contêiner do Linux.
> Se você estiver tentando investigar um contêiner do Windows, use **cmd**.

```xml
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <CodePackagePolicy CodePackageRef="Code">
        <Probes>
          <Probe Type="Liveness" FailureThreshold="5" SuccessThreshold="2" InitialDelaySeconds="10" PeriodSeconds="30" TimeoutSeconds="20">
            <Exec>
              <Command>ping,-c,2,localhost</Command>
            </Exec>
          </Probe>        
       </Probes>
      </CodePackagePolicy>
    </Policies>
  </ServiceManifestImport>
```

## <a name="next-steps"></a>Próximas etapas
Consulte o seguinte artigo para obter informações relacionadas:
* [Service Fabric e contêineres][containers-introduction-link]

<!-- Links -->
[containers-introduction-link]: service-fabric-containers-overview.md
[health-introduction-link]: service-fabric-health-introduction.md
[application-model-link]: service-fabric-application-model.md
[hosting-model-link]: service-fabric-hosting-model.md

