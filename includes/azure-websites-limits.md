---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: ce64047fd7490106790ea8bb1ad7963d82a87c24
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172299"
---
| Resource | Grátis | Compartilhado | Basic | Standard | Premium (v2) | Isolado </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Aplicativos Web, móveis ou de API](https://azure.microsoft.com/services/app-service/) por [plano do serviço de aplicativo do Azure](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup>|
| [Plano do Serviço de Aplicativo](../articles/app-service/overview-hosting-plans.md) |10 por região |10 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos|
| Tipo de instância de computação |Compartilhado |Compartilhado |Dedicado<sup>3</sup> |Dedicado<sup>3</sup> |Dedicado<sup>3</sup></p> |Dedicado<sup>3</sup>|
| [Escalar horizontalmente](../articles/app-service/web-sites-scale.md) (máximo de instâncias) |1 compartilhada |1 compartilhada |3 dedicados<sup>3</sup> |10 dedicados<sup>3</sup> |20 dedicados<sup>3</sup>|100 dedicados<sup>4</sup>|
| Armazenamento<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| Tempo de CPU (5 minutos)<sup>6</sup> |3 minutos |3 minutos |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão|
| Tempo de CPU (dia)<sup>6</sup> |60 minutos |240 minutos |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |
| Memória (1 hora) |1\.024 MB por plano do serviço de aplicativo |1\.024 MB por aplicativo |N/D |N/D |N/D |N/D |
| Largura de banda |165 MB |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |
| Arquitetura do aplicativo |32 bits |32 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |
| Web sockets por instância<sup>7</sup> |5 |35 |350 |Ilimitado |Ilimitado |Ilimitado |
| [Conexões do depurador](../articles/app-service/troubleshoot-dotnet-visual-studio.md) simultâneas por aplicativo |1 |1 |1 |5 |5 |5 |
| Certificados de serviço de aplicativo por assinatura<sup>10</sup>| Sem suporte | Sem suporte |10 |10 |10 |10 |
| Domínios personalizados por aplicativo</a> |0 (somente subdomínio do azurewebsites.net)|500 |500 |500 |500 |500 |
| domínio personalizado [Suporte a SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Sem suporte, o certificado curinga para *. azurewebsites.net disponível por padrão|Sem suporte, o certificado curinga para *. azurewebsites.net disponível por padrão|Conexões SSL de SNI ilimitadas |Conexões SSL de SNI ilimitadas e IP SSL incluídas |Conexões SSL de SNI ilimitadas e IP SSL incluídas | Conexões SSL de SNI ilimitadas e IP SSL incluídas|
| Balanceador de carga integrados | |X |X |X |X |X<sup>9</sup> |
| [Sempre ativo](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Backups agendados](../articles/app-service/manage-backup.md) | | | | Backups agendados a cada 2 horas, um máximo de 12 backups por dia (manual + agendado) | Backups agendados a cada hora, um máximo de 50 backups por dia (manual + agendado) | Backups agendados a cada hora, um máximo de 50 backups por dia (manual + agendado) |
| [Autoescala](../articles/app-service/web-sites-scale.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| [Agendador do Azure](https://azure.microsoft.com/services/scheduler/) | |X |X |X |X |X |
| [Monitoramento do ponto de extremidade](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Slots de preparo](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| Contrato de Nível de Serviço | |  |99,9 % |99,95%|99,95%|99,95%|  

<sup>1</sup>Aplicativos e cotas de armazenamento são oferecidos por plano de Serviço de Aplicativo, a menos que haja indicação contrária.  
<sup>2</sup>O número real de aplicativos que podem ser hospedados nesses computadores depende da atividade dos aplicativos, do tamanho das instâncias do computador e da utilização do recurso correspondente.  
<sup>3</sup>As instâncias dedicadas podem ter tamanhos diferentes. Para saber mais, veja [Preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup>sejam permitidas mais mediante solicitação.  
<sup>5</sup>O limite de armazenamento é o tamanho total do conteúdo em todos os aplicativos no mesmo plano de Serviço de Aplicativo.  
<sup>6</sup>Esses recursos são limitados pelos recursos físicos nas instâncias dedicadas (o tamanho de instância e o número de instâncias).  
<sup>7</sup>Ao escalar um aplicativo na camada Basic para duas instâncias, você tem 350 conexões simultâneas para cada uma das duas instâncias.  
<sup>8</sup>Execute os executáveis personalizados e/ou os scripts sob demanda, por agendamento ou continuamente como uma tarefa em segundo plano na instância do Serviço de Aplicativo. Para a execução contínua de Trabalhos Web, a opção Sempre Ativado é obrigatória. Trabalhos Web agendados requerem o Agendador do Azure Gratuito ou Standard. Não há nenhum limite predefinido no número de trabalhos Web que pode ser executados em uma instância de serviço de aplicativo. Há limites práticos que dependem do que o código do aplicativo está tentando fazer.  
<sup>9</sup>SKUs isolados do serviço de aplicativo têm a capacidade de ser internamente de carga balanceada (ILB) com o Azure Load Balancer, portanto, não há nenhuma conectividade pública da internet. Como resultado, alguns recursos do Serviço de Aplicativo Isolado de ILB devem ser usados de computadores com acesso direto ao ponto de extremidade de rede de ILB.  
<sup>10</sup>pode ser aumentado o limite de cota o certificado do serviço de aplicativo por assinatura por meio de uma solicitação de suporte a um limite máximo de 200.  