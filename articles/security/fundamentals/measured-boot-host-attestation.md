---
title: Firmware medido de inicialização e de atestado de host-segurança do Azure
description: Visão geral técnica do firmware do Azure medido de inicialização e atestado de host.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f4906d4e0590df047bac4ee15cb0e996a59c3d5b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498856"
---
# <a name="measured-boot-and-host-attestation"></a>Atestado de inicialização e host medidos
Este artigo descreve como a Microsoft garante a integridade e a segurança dos hosts por meio do atestado medido de inicialização e host.

## <a name="measured-boot"></a>Inicialização medida

O [Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-top-node) (TPM) é um componente de auditoria criptograficamente seguro e de prova de adulteração com o firmware fornecido por terceiros confiáveis. O log de configuração de inicialização contém medidas de cadeia de hash registradas nos registros de configuração de plataforma (PCR) quando o host sofreu a seqüência de inicialização. A figura a seguir mostra esse processo de gravação. Adicionar incrementalmente uma medição anteriormente com hash ao hash da próxima medição e executar o algoritmo de hash na União realiza o encadeamento de hash.

![Diagrama que mostra a cadeia de hash do serviço de atestado de host.](./media/measured-boot-host-attestation/hash-chaining.png)

O atestado é realizado quando um host fornece uma prova de seu estado de configuração usando seu log de configuração de inicialização (TCGLog). A falsificação de um log de inicialização é difícil porque o TPM não expõe seus valores de PCR além das operações de leitura e extensão. Além disso, as credenciais fornecidas pelo serviço de atestado de host são lacradas para valores de PCR específicos. O uso de encadeamento de hash torna a computação impraticável para falsificar ou deslacrar as credenciais fora de banda.

## <a name="host-attestation-service"></a>Serviço de atestado de host

O serviço de atestado de host é uma medida preventiva que verifica se as máquinas host são confiáveis antes de terem permissão para interagir com os dados do cliente ou cargas de trabalho. O serviço de atestado de host verifica Validando uma instrução de conformidade (prova verificável da conformidade do host) enviada por cada host em relação a uma política de atestado (definição do estado de segurança). A integridade desse sistema é garantida por uma [raiz de confiança](https://www.uefi.org/sites/default/files/resources/UEFI%20RoT%20white%20paper_Final%208%208%2016%20%28003%29.pdf) fornecida por um TPM.

O serviço de atestado de host está presente em cada cluster do Azure em um ambiente bloqueado especializado. O ambiente bloqueado inclui outros serviços de gatekeeper que participam do protocolo de inicialização de máquina host. Uma PKI (infraestrutura de chave pública) atua como um intermediário para validar a comprovação de solicitações de atestado e como um emissor de identidade (contingente após o atestado de host bem-sucedido). As credenciais de pós-atestado emitidas para o host de atestado são lacradas para sua identidade. Somente o host solicitante pode deslacrar as credenciais e aproveitá-las para obter permissões incrementais. Isso impede contra ataques de interceptação e de falsificação.

Se um host do Azure chegar da fábrica com uma configuração incorreta de segurança ou violado no datacenter, seu TCGLog conterá indicadores de comprometimento sinalizados pelo serviço de atestado de host no próximo atestado, o que causará uma falha de atestado. Falhas de atestado impedem que a frota do Azure confie no host incorreto. Essa prevenção bloqueia efetivamente todas as comunicações de e para o host e dispara um fluxo de trabalho de incidentes. A investigação e uma análise detalhada do post-mortem são realizadas para determinar as causas raiz e as possíveis indicações de comprometimento. Só depois que a análise é concluída, o host é corrigido e tem a oportunidade de ingressar na frota do Azure e realizar cargas de trabalho do cliente.

Veja a seguir uma arquitetura de alto nível do serviço de atestado de host:

![Diagrama que mostra a arquitetura do serviço de atestado de host.](./media/measured-boot-host-attestation/host-attestation-arch.png)

## <a name="attestation-measurements"></a>Medições de atestado

Veja a seguir exemplos de muitas medições capturadas hoje.

### <a name="secure-boot-and-secure-boot-keys"></a>Inicialização segura e chaves de inicialização segura
Ao validar que os resumos do banco de dados de assinatura e das assinaturas revogadas estão corretos, o serviço de atestado de host garante que o agente cliente considere o software certo como confiável. Ao validar as assinaturas do banco de dados da chave de registro de chave pública e da chave de plataforma pública, o serviço de atestado de host confirma que somente partes confiáveis têm permissão para modificar as definições de qual software é considerado confiável. Por fim, ao garantir que a inicialização segura esteja ativa, o serviço de atestado de host valida que essas definições estão sendo impostas.

### <a name="debug-controls"></a>Controles de depuração
Os depuradores são ferramentas poderosas para desenvolvedores. No entanto, o acesso de irrestrito à memória e a outros comandos de depuração poderia enfraquecer a proteção de dados e a integridade do sistema, se fornecido a uma parte não confiável. O serviço de atestado de host garante que qualquer tipo de depuração esteja desabilitado na inicialização em máquinas de produção.

### <a name="code-integrity"></a>Integridade de código
A [inicialização segura](secure-boot.md) de UEFI garante que apenas softwares confiáveis de nível baixo possam ser executados durante a sequência de inicialização. No entanto, as mesmas verificações também devem ser aplicadas no ambiente de pós-inicialização a drivers e outros executáveis com acesso de modo kernel. Para esse fim, uma política de CI (integridade de código) é usada para definir quais drivers, binários e outros executáveis são considerados confiáveis, especificando assinaturas válidas e inválidas. Essas políticas são impostas. Violações de política geram alertas para a equipe de resposta a incidentes de segurança para investigação.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que fazemos para impulsionar a integridade e a segurança da plataforma, consulte:

- [Segurança do firmware](firmware.md)
- [Inicialização segura](secure-boot.md)
- [Projeto Cerberus](project-cerberus.md)
- [Criptografia em repouso](encryption-atrest.md)
- [Segurança do hipervisor](hypervisor.md)