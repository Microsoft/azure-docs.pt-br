---
title: Componentes e limites do sistema de informações do Azure
description: Este artigo fornece uma descrição geral da arquitetura e do gerenciamento do Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: b390dc9bd2b690837a85a5bab361a534b9c9d5a5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587187"
---
# <a name="azure-information-system-components-and-boundaries"></a>Componentes e limites do sistema de informações do Azure
Este artigo apresenta uma descrição geral da arquitetura e do gerenciamento do Azure. O ambiente do sistema do Azure é composto por redes a seguir:

- Rede de produção do Microsoft Azure (rede do Azure)
- Rede corporativa da Microsoft (corpnet)

Equipes de TI separadas são responsáveis por operações e manutenção dessas redes.

## <a name="azure-architecture"></a>Arquitetura do Azure
O Azure é uma plataforma de computação em nuvem e uma infraestrutura para compilar, implantar e gerenciar aplicativos e serviços por meio de uma rede de datacenters. A Microsoft gerencia esses data centers. Com base no número de recursos que você especificar, o Azure criará VMs (máquinas virtuais) com conforme a necessidade de recursos. Essas VMs são executadas em um hipervisor do Azure, que é projetado para uso na nuvem e não está acessível ao público.

Em cada nó de servidor físico do Azure, há um hipervisor executado diretamente sobre o hardware. O hipervisor divide um nó em um número variável de VMs convidadas. Cada nó também tem uma VM raiz, que executa o sistema operacional do host. Firewall do Windows está habilitado em cada VM. Você define quais portas são endereçáveis configurando o arquivo de definição de serviço. Essas portas são as únicas abertas e endereçáveis interna ou externamente. Todo o tráfego e acesso ao disco e à rede é mediado pelo hipervisor e pelo sistema operacional raiz.

Na camada do host, as VMs do Azure que executam uma versão robusta e personalizada do Windows Server mais recente. O Azure usa uma versão do Windows Server que inclui somente os componentes necessários para hospedar as VMs. Isso melhora o desempenho e reduz a superfície de ataque. Limites de computador são impostos pelo hipervisor, que não depende da segurança do sistema operacional.

### <a name="azure-management-by-fabric-controllers"></a>Gerenciamento do Azure por controladores de malha

No Azure, VMs em execução em servidores físicos (nós/folhas) são agrupadas em clusters de aproximadamente 1.000. As VMs são gerenciadas independentemente por um componente de software de plataforma expandido e redundante chamado de FC (controlador de malha).

Cada FC gerencia o ciclo de vida de aplicativos em execução no seu cluster e provisiona e monitora a integridade do hardware sob seu controle. Ele executa operações autônomas, como reencarnar instâncias de VM nos servidores íntegros quando determina que um servidor falhou. O FC também executa operações de gerenciamento de aplicativos, como implantar, atualizar e escalar horizontalmente os aplicativos.

O data center é dividido em clusters. Clusters isolam falhas no nível do FC e impedem que determinadas classes de erros afetem os servidores além do cluster em que elas ocorrem. FCs que servem a um cluster do Azure específico são agrupados em um cluster de FC.

### <a name="hardware-inventory"></a>Inventário de hardware

O FC prepara um inventário dos dispositivos de hardware e de rede do Azure durante o processo de configuração de inicialização. Qualquer novo hardware e os componentes de rede inserindo o ambiente de produção do Azure devem seguir o processo de configuração de inicialização. O FC é responsável por gerenciar o estoque inteiro listado no arquivo de configuração datacenter.xml.

### <a name="fc-managed-operating-system-images"></a>Imagens do sistema operacional gerenciado pelo FC

A equipe do sistema operacional fornece imagens, na forma de Discos Rígidos Virtuais, implantadas em todas as VMs de host e convidadas no ambiente de produção do Azure. A equipe de sistema operacional constrói essas imagens de base por meio de um processo automatizado de compilação offline. A imagem de base é uma versão do sistema operacional em que o kernel e outros componentes básicos foram modificados e otimizados para dar suporte ao ambiente do Azure.

Há três tipos de imagens do sistema operacional gerenciado por malha:

- Host: Um sistema operacional personalizado executado nas VMs de host.
- Nativo: Um sistema operacional nativo executado nos locatários (por exemplo, o Armazenamento do Azure). Esse sistema operacional não tem nenhum hipervisor.
- Convidado: Um sistema operacional convidado executado nas VMs convidadas.

Os sistemas operacionais gerenciados por FC de host e nativos são projetados para uso na nuvem e não são acessíveis publicamente.

#### <a name="host-and-native-operating-systems"></a>Sistemas operacionais de host e nativos

Host e nativo são imagens do sistema operacional reforçadas que hospedam os agentes de malha e são executados em um nó de computação (executados como a primeira VM no nó) e nós de armazenamento. A vantagem de usar imagens otimizadas de base do host e nativas é reduzir a área de superfície exposta por APIs ou componentes não utilizados. Podem impor riscos elevados à segurança e aumentar a superfície do sistema operacional. Sistemas operacionais de volume reduzido incluem apenas os componentes necessários para o Azure.

#### <a name="guest-operating-system"></a>Sistema operacional convidado

Componentes internos do Azure em execução em VMs de sistema operacional convidado não têm oportunidade de executar o protocolo RDP. Todas as alterações às definições de configuração de linha de base devem passar pelo processo de gerenciamento de versão e mudança.

## <a name="azure-datacenters"></a>Datacenters do Azure
A equipe MCIO (Infraestrutura e Operações de Nuvem da Microsoft) gerencia as instalações do datacenter e a infraestrutura física para todos os serviços online da Microsoft. MCIO é responsável principalmente pelo gerenciamento dos controles físicos e ambientais dentro dos datacenters, bem como por gerenciar e dar suporte a dispositivos de rede de perímetro externo (como roteadores de borda e roteadores de datacenter). MCIO também é responsável por configurar o hardware de servidor mínimo em racks nos data centers. Os clientes não têm nenhuma interação direta com o Azure.

## <a name="service-management-and-service-teams"></a>Equipes de serviço e gerenciamento de serviço
Vários grupos de engenharia, conhecidos como equipes de serviço, gerenciam o suporte do serviço do Azure. Cada equipe de serviço é responsável por uma área do suporte para o Azure. Cada equipe de serviço deve disponibilizar um engenheiro 24 horas por dia, 7 dias por semana para investigar e resolver falhas no serviço. Por padrão, as equipes de serviço não têm acesso físico ao hardware operacional no Azure.

As equipes de serviço são:

- Plataforma de aplicativo
- Azure Active Directory
- Computação do Azure
- Rede do Azure
- Engenharia de serviços de nuvem
- ISSD: Segurança
- Autenticação multifator
- Banco de dados SQL
- Armazenamento

## <a name="types-of-users"></a>Tipos de usuários
Funcionários (ou prestadores de serviço) da Microsoft são considerados usuários internos. Todos os outros são considerados usuários externos. Todos os usuários internos do Azure têm seus status de funcionário categorizados com um nível de confidencialidade que define o acesso aos dados do cliente (acesso ou sem acesso). Os privilégios de usuário para o Azure (permissão de autorização após a autenticação) são descritos na tabela a seguir:

| Função | Interno ou externo | Nível de confidencialidade | Privilégios autorizados e funções executadas | Tipo de acesso
| --- | --- | --- | --- | --- |
| Engenheiro do datacenter do Azure | Interna | Nenhum acesso aos dados do cliente | Gerenciar a segurança física das instalações. Conduzir patrulhas dentro e fora do datacenter e monitorar todos os pontos de entrada. Escoltar a entrada e saída do datacenter de determinadas pessoas sem liberação que prestam serviços gerais (por exemplo, alimentação ou limpeza) ou trabalho IT dentro do datacenter. Realizar monitoramento e manutenção de rotina de hardware de rede. Executar gerenciamento de incidente e trabalho de interrupção/reparo usando uma variedade de ferramentas. Realizar o monitoramento e a manutenção de rotina do hardware físico nos datacenters. Acesso ao ambiente sob demanda de proprietários de propriedade. Capaz de realizar investigações jurídicas, registrar em log relatórios de incidentes e exigir requisitos de política e treinamento de segurança obrigatórios. Propriedade operacional e manutenção de ferramentas de segurança críticas, como scanners e coleta de log. | Acesso persistente ao ambiente. |
| Triagem de incidentes do Azure (engenheiros de resposta rápida) | Interna | Acesso aos dados do cliente | Gerenciar as comunicações entre o MCIO, o suporte e as equipes de engenharia. Realizar a triagem de incidentes de plataforma, problemas de implantação e solicitações de serviço. | Acesso just-in-time ao ambiente – com acesso persistente limitado para sistemas não clientes. |
| Engenheiros de implantação do Azure | Interna | Acesso aos dados do cliente | Implantar e atualizar os componentes da plataforma, software e alterações de configuração agendadas em suporte ao Azure. | Acesso just-in-time ao ambiente – com acesso persistente limitado para sistemas não clientes. |
| Suporte de interrupção de cliente do Azure (locatário) | Interna | Acesso aos dados do cliente | Depurar e diagnosticar falhas e interrupções de plataforma para locatários de computação individual e contas do Azure. Analisar falhas. Conduzir correções críticas para a plataforma ou o cliente e conduzir aprimoramentos técnicos em todo o suporte. | Acesso just-in-time ao ambiente – com acesso persistente limitado para sistemas não clientes. |
| Incidente e engenheiros de sites ativos do Azure (engenheiros de monitoramento) | Interna | Acesso aos dados do cliente | Diagnosticar e mitigar a integridade de plataforma usando ferramentas de diagnóstico. Conduzir correções para os drivers de volume da unidade, reparar itens resultantes de interrupções e ajudar em ações de restauração de interrupção. | Acesso just-in-time ao ambiente – com acesso persistente limitado para sistemas não clientes. |
|Clientes do Azure | Externo | N/D | N/D | N/D |

O Azure usa identificadores exclusivos para autenticar usuários da organização e clientes (ou processos atuando em nome dos usuários organizacionais). Isso se aplica a todos os ativos e dispositivos que fazem parte do ambiente do Azure.

### <a name="azure-internal-authentication"></a>Autenticação interna do Azure

As comunicações entre componentes internos do Azure são protegidas com criptografia TLS. Na maioria dos casos, os certificados x. 509 são autoassinados. Certificados com conexões que podem ser acessados de fora da rede do Azure são uma exceção, assim como certificados para o FCs. FCs têm certificados emitidos por um Microsoft Certificate da autoridade (CA) que é apoiada por uma autoridade de certificação de raiz confiável. Isso permite que as chaves públicas de FC ser revertidas facilmente. Além disso, ferramentas para desenvolvedores Microsoft usam chaves públicas de FC. Quando os desenvolvedores enviam novas imagens de aplicativo, as imagens são criptografadas com uma chave pública de FC para proteger os segredos incorporados.

### <a name="azure-hardware-device-authentication"></a>Autenticação de dispositivo de hardware do Azure

O FC mantém um conjunto de credenciais (chaves e/ou senhas) usadas para autenticar-se em vários dispositivos de hardware sob seu controle. A Microsoft usa um sistema para impedir o acesso a essas credenciais. Especificamente, o transporte, a persistência e o uso dessas credenciais foram projetados para impedir que desenvolvedores, administradores e pessoal e serviços de backup do Azure acessem informações confidenciais, sigilosas ou privadas.

A Microsoft usa a criptografia baseada em chave pública de identidade mestre do FC. Isso acontece na instalação do FC e nos tempos de reconfiguração do FC para transferir as credenciais usadas para acessar dispositivos de hardware de rede. Quando o FC precisa das credenciais, ele as recupera e descriptografa.

### <a name="network-devices"></a>Dispositivos de rede

A equipe de rede do Azure configura contas de serviço de rede para permitir que um cliente do Azure autentique-se em dispositivos de rede (roteadores, comutadores e balanceadores de carga).

## <a name="secure-service-administration"></a>Administração segura do serviço
A equipe de operações do Azure deve usar SAWs (estações de trabalho de administração seguras). Os clientes podem implementar controles semelhantes usando estações de trabalho com acesso privilegiado. Com SAWs, a equipe administrativa usa uma conta administrativa individualmente atribuída separada da conta de usuário padrão do usuário. A SAW se baseia nessa prática de separação de conta fornecendo uma estação de trabalho confiável para essas contas confidenciais.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, consulte:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento de infraestrutura do Microsoft Azure](azure-infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente do Microsoft Azure](azure-protection-of-customer-data.md)
