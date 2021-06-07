---
title: Solucionar problemas de desempenho de registro
description: Sintomas, causas e resolução de problemas comuns com o desempenho de um registro
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 15129ebe1da2e52fac106a34863f609c440549ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148416"
---
# <a name="troubleshoot-registry-performance"></a>Solucionar problemas de desempenho de registro

Este artigo ajuda você a solucionar problemas que podem ser encontrados com o desempenho de um registro de contêiner do Azure. 

## <a name="symptoms"></a>Sintomas

Pode incluir um ou mais dos seguintes:

* As imagens pull ou push com a CLI do Docker demoram mais do que o esperado
* A implantação de imagens em um serviço como o serviço kubernetes do Azure leva mais tempo do que o esperado
* Não é possível concluir um grande número de operações simultâneas de pull ou push no tempo esperado
* As operações de pull ou push em um registro replicado geograficamente demoram mais do que o esperado, ou o push falha com erro `Error writing blob` ou `Error writing manifest`

## <a name="causes"></a>Causas

* Sua velocidade de conexão de rede pode retardar as operações de registro- [solução](#check-expected-network-speed)
* A compactação ou a extração da camada de imagem pode estar lenta na [solução](#check-client-hardware) cliente  
* Você está atingindo um limite configurado na camada de serviço do registro ou na [solução](#review-configured-limits) de ambiente
* Seu registro com replicação geográfica tem réplicas em regiões próximas – [solução](#configure-geo-replicated-registry)
* Você está extraindo de uma réplica de registro distante geograficamente- [solução](#configure-dns-for-geo-replicated-registry)

Se você não resolver o problema aqui, consulte [solução de problemas avançada](#advanced-troubleshooting) e [próximas etapas](#next-steps) para outras opções.

## <a name="potential-solutions"></a>Possíveis soluções

### <a name="check-expected-network-speed"></a>Verificar a velocidade de rede esperada

Verifique sua velocidade de carregamento e download da Internet ou use uma ferramenta como AzureSpeed para testar o [upload](https://www.azurespeed.com/Azure/Uploadß) e o [Download](https://www.azurespeed.com/Azure/Download) do armazenamento de BLOBs do Azure, que hospeda camadas de imagem do registro.

Verifique o tamanho da imagem em relação ao tamanho máximo com suporte e o download com suporte ou a largura de banda de upload para a camada de serviço do registro. Se o registro estiver na camada básica ou Standard, considere atualizar para melhorar o desempenho. 

Para a implantação de imagem em outros serviços, verifique as regiões onde o registro e o destino estão localizados. Considere localizar o registro e o destino de implantação na mesma região ou em regiões de fechamento de rede para melhorar o desempenho.

Links relacionados:

* [Criar camadas de serviço do Registro de Contêiner do Azure](container-registry-skus.md)    
* [Perguntas frequentes do registro de contêiner](container-registry-faq.md)
* [Metas de desempenho e escalabilidade para o armazenamento de BLOBs do Azure](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>Verificar o hardware do cliente

O tipo de disco e a CPU no cliente do Docker podem afetar a velocidade de extração ou compactação de camadas de imagem no cliente como parte das operações pull ou push. Por exemplo, a extração de camada em uma unidade de disco rígido levará mais tempo do que em um disco de estado sólido. Compare as operações de pull para imagens comparáveis do seu registro de contêiner do Azure e de um registro público, como o Hub do Docker.

### <a name="review-configured-limits"></a>Examinar limites configurados

Se você estiver simultaneamente enviando ou extraindo várias ou várias imagens de várias camadas para o registro, examine os limites de ReadOps e WriteOps com suporte para a camada de serviço do registro. Se o registro estiver na camada básica ou Standard, considere atualizar para aumentar os limites. Verifique também com seu provedor de rede sobre a limitação de rede que pode ocorrer com muitas operações simultâneas. 

Examine a configuração do daemon do Docker para obter o máximo de uploads ou downloads simultâneos para cada operação de Push ou pull no cliente. Configure limites mais altos, se necessário.

Como cada camada de imagem requer uma operação de leitura ou gravação de registro separada, verifique o número de camadas em suas imagens. Considere as estratégias para reduzir o número de camadas de imagem.

Links relacionados:

* [Criar camadas de serviço do Registro de Contêiner do Azure](container-registry-skus.md)
* [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>Configurar o registro replicado geograficamente

Um cliente do Docker que envia uma imagem para um registro com replicação geográfica pode não enviar por push todas as camadas de imagem e seu manifesto para uma única região replicada. Essa situação pode ocorrer porque o Gerenciador de tráfego do Azure roteia solicitações de registro para o registro replicado mais próximo da rede. Se o registro tiver duas regiões de replicação próximas, as camadas de imagem e o manifesto poderão ser distribuídos para os dois sites, e a operação de push falhará quando o manifesto for validado.

Para otimizar a resolução DNS para a réplica mais próxima ao efetuar push de imagens, configure um registro com replicação geográfica nas mesmas regiões do Azure da origem das operações de push ou a região mais próxima ao trabalhar fora do Azure.

Para solucionar problemas de operações com um registro replicado geograficamente, você também pode desabilitar temporariamente o roteamento do Gerenciador de tráfego para uma ou mais replicações.

Links relacionados:

* [Replicação geográfica no Registro de Contêiner do Azure](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>Configurar o DNS para o registro replicado geograficamente

Se as operações de pull de um registro com replicação geográfica aparecerem lentas, a configuração de DNS no cliente poderá ser resolvida para um servidor DNS geograficamente distante. Nesse caso, o Gerenciador de tráfego pode estar roteando solicitações para uma réplica que está perto da rede para o servidor DNS, mas distante do cliente. Execute uma ferramenta como `nslookup` ou `dig` (no Linux) para determinar a réplica para a qual o Gerenciador de tráfego roteia as solicitações de registro. Por exemplo:

```console
nslookup myregistry.azurecr.io
```

Uma solução potencial é configurar um servidor DNS mais próximo.

Links relacionados:

* [Replicação geográfica no Registro de Contêiner do Azure](container-registry-geo-replication.md)
* [Solução de problemas de operações de push com registros com replicação geográfica](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [Desabilitar temporariamente o roteamento para replicação](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [Perguntas frequentes do Gerenciador de tráfego](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>Solução de problemas avançada

Se suas permissões para recursos do registro permitirem, [Verifique a integridade do ambiente do registro](container-registry-check-health.md). Se forem relatados erros, examine a [referência de erro](container-registry-health-error-reference.md) para obter possíveis soluções.

Se a [coleção de logs de recursos](container-registry-diagnostics-audit-logs.md) estiver habilitada no registro, examine o log ContainterRegistryRepositoryEvents. Esse log armazena informações para operações como eventos de Push ou pull. Consulte o log para obter [falhas de operação em nível de repositório](container-registry-diagnostics-audit-logs.md#repository-level-operation-failures). 

Links relacionados:

* [Logs para avaliação de diagnóstico e auditoria](container-registry-diagnostics-audit-logs.md)
* [Perguntas frequentes do registro de contêiner](container-registry-faq.md)
* [Melhores práticas para o Registro de Contêiner do Azure](container-registry-best-practices.md)

## <a name="next-steps"></a>Próximas etapas

Se você não resolver o problema aqui, consulte as opções a seguir.

* Outros tópicos de solução de problemas de registro incluem:
  * [Solucionar problemas de logon do registro](container-registry-troubleshoot-login.md)
  * [Solucionar problemas de rede com o registro](container-registry-troubleshoot-access.md)
* Opções de [suporte da Comunidade](https://azure.microsoft.com/support/community/)
* [P e R da Microsoft](/answers/products/)
* [Abra um tíquete de suporte](https://azure.microsoft.com/support/create-ticket/)