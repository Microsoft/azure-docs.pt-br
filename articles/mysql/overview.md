---
title: Visão geral – Banco de Dados do Azure para MySQL
description: Saiba mais sobre o Banco de Dados do Azure para MySQL, um serviço de banco de dados relacional no Microsoft Cloud baseado no MySQL Community Edition.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 3/18/2020
ms.openlocfilehash: 20b54afdfbb5641def921ffc17b3c38f6650acab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101736212"
---
# <a name="what-is-azure-database-for-mysql"></a>O que é o Banco de Dados do Azure para MySQL?

O Banco de Dados do Azure para MySQL é um serviço de banco de dados relacional no Microsoft Cloud com base no mecanismo de banco de dados [MySQL Community Edition](https://www.mysql.com/products/community/) (disponível sob a licença GPLv2), versões 5.6, 5.7 e 8.0. O Banco de Dados do Azure para MySQL fornece:

- Alta disponibilidade interna.
- Proteção de dados usando backups automáticos e restauração pontual por até 35 dias.
- Manutenção automatizada para hardware, sistema operacional e mecanismo de banco de dados subjacentes para manter o serviço seguro e atualizado.
- Desempenho previsível, com preços pré-pagos inclusivos.
- Dimensionamento elástico em segundos.
- Controles de otimização de custos com capacidade de parar/iniciar o servidor. 
- Segurança de nível corporativo e conformidade líder do setor para proteger dados confidenciais em repouso e em movimento.
- Monitoramento e automação para simplificar o gerenciamento e o monitoramento para implantações em larga escala.
- Experiência de suporte líder do setor.

Esses recursos não precisam de quase nenhuma administração e todos são fornecidos sem nenhum custo adicional. Eles permitem que você se concentre no desenvolvimento rápido de aplicativos e em acelerar seu tempo de colocação no mercado, em vez de alocar tempo e recursos preciosos ao gerenciamento de máquinas virtuais e de infraestrutura. Além disso, você pode continuar desenvolvendo seu aplicativo com a plataforma e as ferramentas de software livre de sua escolha e pode fornecê-lo com a velocidade e a eficiência que sua empresa exige, tudo isso sem precisar aprender novas habilidades.

:::image type="content" source="media/overview/1-azure-db-for-mysql-conceptual-diagram.png" alt-text="Diagrama conceitual do Banco de Dados do Azure para MySQL":::

## <a name="deployment-models"></a>Modelos de implantação

O Banco de Dados do Azure para MySQL da plataforma MySQL Community Edition está disponível em dois modos de implantação:
- Servidor único 
- Servidor Flexível (versão prévia)
  
### <a name="azure-database-for-mysql---single-server"></a>Banco de Dados do Azure para MySQL – Servidor Único

O Banco de Dados do Azure para MySQL Single Server é um serviço de banco de dados totalmente gerenciado com requisitos mínimos para personalizações do banco de dados. A plataforma de servidor único é projetada para administrar a maioria das funções de gerenciamento de banco de dados, como aplicação de patches, backups, alta disponibilidade, controle e segurança com mínima configuração do usuário. A arquitetura é otimizada para alta disponibilidade interna com disponibilidade de 99,99% em uma zona de disponibilidade única. Ele dá suporte à versão da comunidade do MySQL 5.6, 5.7 e 8.0. O serviço está em disponibilidade geral em diversas [regiões do Azure](https://azure.microsoft.com/global-infrastructure/services/).

A opção de implantação Servidor Único oferece três tipos de preço: Básico, Uso Geral e Otimizado para Memória. Cada tipo oferece recursos diferentes para dar suporte a suas cargas de trabalho do banco de dados. Você pode criar seu primeiro aplicativo em um banco de dados pequeno por alguns dólares por mês e então ajustar a escala para atender às necessidades da sua solução. A escalabilidade dinâmica permite que o banco de dados responda de forma transparente a mudanças rápidas nos requisitos de recursos. Você paga apenas pelos recursos de que precisa, e somente quando precisa deles. Veja [Tipos de preço](concepts-pricing-tiers.md) para obter detalhes.

Servidores únicos são mais adequados para aplicativos nativos na nuvem projetados para lidar com aplicação de patch automatizada sem a necessidade de controle granular sobre o agendamento de aplicação de patch e definições de configuração de MySQL personalizadas. 

Para obter uma visão geral detalhada do modo de implantação de servidor único, confira [visão geral de servidor único](single-server-overview.md).

### <a name="azure-database-for-mysql---flexible-server-preview"></a>Banco de Dados do Azure para MySQL – Servidor Flexível (versão prévia)

O Servidor Flexível do Banco de Dados do Azure para MySQL é um serviço de banco de dados totalmente gerenciado projetado para fornecer controle e flexibilidade mais granulares nas funções de gerenciamento de banco de dados e definições de configuração. Em geral, o serviço fornece mais flexibilidade e personalizações conforme os requisitos do usuário. A arquitetura de servidor flexível permite que os usuários optem por alta disponibilidade dentro de uma zona de disponibilidade única e entre várias zonas de disponibilidade. Os servidores flexíveis fornecem melhores controles de otimização de custos com a capacidade de parar/iniciar o servidor e a camada de computação expansível, ideal para cargas de trabalho que não precisam de capacidade de computação completa continuamente. O serviço dá suporte à versão da comunidade do MySQL 5.7 e 8.0. O serviço está em versão prévia pública, disponível em diversas [regiões do Azure](https://azure.microsoft.com/global-infrastructure/services/).

Os servidores flexíveis são mais adequados para 
- Desenvolvimentos de aplicativos que exigem controle e personalizações melhores.
- Alta disponibilidade com redundância de zona
- Janelas de manutenção gerenciadas

Para obter uma visão geral detalhada do modo de implantação de servidor flexível, confira [visão geral do servidor flexível](flexible-server/overview.md).

## <a name="contacts"></a>Contatos
Para quaisquer dúvidas ou sugestões que você possa ter sobre como trabalhar com o Banco de Dados do Azure para MySQL, envie um e-mail para o Banco de Dados do Azure para a Equipe do MySQL ([@Ask Azure DB for MySQL ](mailto:AskAzureDBforMySQL@service.microsoft.com)). Esse endereço de email não é um alias de suporte técnico.

Além disso, considere os seguintes pontos de contato, conforme apropriado:

- Para entrar em contato com o Suporte do Azure, [crie um tíquete no Portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corrigir um problema com sua conta, apresente uma [solicitação de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para fornecer comentários ou solicitar novos recursos, crie uma entrada por meio do [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os dois modos de implantação para o Banco de Dados do Azure para MySQL e escolha as opções corretas conforme suas necessidades.

- [Servidor único](single-server/index.yml)
- [Servidor Flexível](flexible-server/index.yml)
- [Escolha a opção de implantação certa do MySQL para sua carga de trabalho](select-right-deployment-type.md)
