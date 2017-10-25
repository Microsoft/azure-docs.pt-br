| Recurso | Grátis | Compartilhado (Visualização) | Basic | Padrão | Premium (Visualização)</th> |
| --- | --- | --- | --- | --- | --- |
| [Aplicativos Web, móveis ou de API](https://azure.microsoft.com/services/app-service/) por [plano do Serviço de Aplicativo](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup> |10 |100 |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |Ilimitado<sup>2</sup> |
| [Aplicativos lógicos](https://azure.microsoft.com/services/app-service/logic/) por [plano do Serviço de Aplicativo](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup> |10 |10 |10 |20 por núcleo |20 por núcleo |
| [Plano do Serviço de Aplicativo](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |1 por região |10 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |100 por grupo de recursos |
| Tipo de instância de computação |Compartilhado |Compartilhado |Dedicado<sup>3</sup> |Dedicado<sup>3</sup> |Dedicado<sup>3</sup></p> |
| [Escalabilidade](../articles/app-service/web-sites-scale.md) (máximo de instâncias) |1 compartilhada |1 compartilhada |3 dedicados<sup>3</sup> |10 dedicados<sup>3</sup> |20 dedicados (50 em ASE)<sup>3,4</sup> |
| Armazenamento<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |500 GB<sup>4,5</sup></p> |
| Tempo de CPU (5 min)<sup>6</sup> |3 minutos |3 minutos |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das tarifas padrão |Ilimitado, pagamento das tarifas padrão |
| Tempo de CPU (dia)<sup>6</sup> |60 minutos |240 minutos |Ilimitado, pagamento das [tarifas](https://azure.microsoft.com/pricing/details/app-service/)</a> padrão |Ilimitado, pagamento das tarifas padrão |Ilimitado, pagamento das tarifas padrão |
| Memória (1 hora) |1024 MB por plano de Serviço de Aplicativo |1024 MB por aplicativo |N/D |N/D |N/D |
| Largura de banda |165 MB |Ilimitada, aplicam-se [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/) |Ilimitado, aplicam-se taxas de transferência de dados |Ilimitado, aplicam-se taxas de transferência de dados |Ilimitado, aplicam-se taxas de transferência de dados |
| Arquitetura do aplicativo |32 bits |32 bits |32 bits/64 bits |32 bits/64 bits |32 bits/64 bits |
| Soquetes Web por instância<sup>7</sup> |5 |35 |350 |Ilimitado |Ilimitado |
| [Conexões do depurador](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md) simultâneas por aplicativo |1 |1 |1 |5 |5 |
| [subdomínio azurewebsites.net com FTP/S e SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |X |X |X |X |X |
| [domínio personalizado](../articles/app-service/app-service-web-tutorial-custom-domain.md)  | |X |X |X |X |
| domínio personalizado [Suporte a SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) | | |Ilimitado |Ilimitado, 5 conexões SSL SNI e 1 conexão IP SSL incluídas |Ilimitado, 5 conexões SSL SNI e 1 conexão IP SSL incluídas |
| Balanceador de carga integrado | |X |X |X |X |
| [Sempre ativo](../articles/app-service/web-sites-configure.md) | | |X |X |X |
| [Backups agendados](../articles/app-service/web-sites-backup.md) | | | |Uma vez por dia |Uma vez a cada 5 minutos<sup>8</sup> |
| [Dimensionamento automático](../articles/app-service/web-sites-scale.md) | | |X |X |X |
| [WebJobs](../articles/app-service/web-sites-create-web-jobs.md)<sup>9</sup> |X |X |X |X |X |
| [Agendador do Azure](https://azure.microsoft.com/services/scheduler/)  | |X |X |X |X |
| [Monitoramento do ponto de extremidade](../articles/app-service/web-sites-monitor.md) | | |X |X |X |
| [Slots de preparação](../articles/app-service/web-sites-staged-publishing.md) | | | |5 |20 |
| Domínios personalizados por aplicativo</a> | |500 |500 |500 |500 |
| Contrato de Nível de Serviço | |<p> |99,9% |99,95%<sup>10</sup> |99,95%<sup>10</sup> |

<sup>1</sup>Aplicativos e cotas de armazenamento são oferecidos por plano de Serviço de Aplicativo, a menos que haja indicação contrária.  
<sup>2</sup>O número real de aplicativos que podem ser hospedados nesses computadores depende da atividade dos aplicativos, do tamanho das instâncias do computador e da utilização do recurso correspondente.  
<sup>3</sup>As instâncias dedicadas podem ter tamanhos diferentes. Confira [Preços do Serviço de Aplicativo](https://azure.microsoft.com/pricing/details/data-transfers/pricing/details/app-service/) para obter mais detalhes.  
<sup>4</sup>A camada Premium permite até 50 instâncias de computação (sujeitas à disponibilidade) e 500 GB de espaço em disco no uso de Ambientes do Serviço de Aplicativo; caso contrário, 20 instâncias de computação e 250 GB de armazenamento.  
<sup>5</sup>O limite de armazenamento é o tamanho total do conteúdo em todos os aplicativos no mesmo plano de Serviço de Aplicativo. Mais opções de armazenamento estão disponíveis em [Ambiente do Serviço de Aplicativo](../articles/app-service/environment/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup>Esses recursos são limitados pelos recursos físicos nas instâncias dedicadas (o tamanho de instância e o número de instâncias).  
<sup>7</sup>Ao escalar um aplicativo na camada Basic para duas instâncias, você tem 350 conexões simultâneas para cada uma das duas instâncias.  
<sup>8</sup>A camada Premium permite que os intervalos de backup sejam reduzidos até a cada 5 minutos no uso de Ambientes do Serviço de Aplicativo; caso contrário, serão 50 vezes por dia  
<sup>9</sup>Execute os executáveis personalizados e/ou os scripts sob demanda, por agendamento ou continuamente como uma tarefa em segundo plano na instância do Serviço de Aplicativo. Para a execução contínua de Trabalhos Web, a opção Sempre Ativado é obrigatória. Trabalhos Web agendados requerem o Agendador do Azure Gratuito ou Standard. Não há nenhum limite predefinido na quantidade de WebJobs que podem ser executados em uma instância do Serviço de Aplicativo, mas há limites práticos que dependem do que o código do aplicativo está tentando fazer.   
<sup>10</sup>SLA de 99,95% fornecido para implantações que usam várias instâncias com o Gerenciador de Tráfego do Azure configurado para failover.  

