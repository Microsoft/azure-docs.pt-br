A tabela a seguir descreve cada uma das principais cotas, limites, padrões e limitações no Agendador do Azure.

| Recurso | Descrição de limite |
| --- | --- |
| **Tamanho do trabalho** |O tamanho máximo do trabalho é 16K. Se um PUT ou PATCH resultarem em um trabalho maior do que esses limites, é retornado um código de status 400 Solicitação incorreta. |
| **Tamanho da solicitação de URL** |O tamanho máximo da URL da solicitação é de 2048 caracteres. |
| **Tamanho do cabeçalho de agregação** |O tamanho máximo do cabeçalho agregação é de 4096 caracteres. |
| **Contagem de cabeçalho** |A contagem máxima do cabeçalho é de 50 cabeçalhos. |
| **Tamanho do corpo** |O tamanho máximo do corpo é de 8192 caracteres. |
| **Intervalo de recorrência** |O período máximo de recorrência é de 18 meses. |
| **Hora de início da hora** |O "tempo para a hora de início" máximo é de 18 meses. |
| **Histórico de trabalho** |O corpo de resposta máximo armazenado no histórico de trabalho é de 2048 bytes. |
| **Frequência** |A cota de frequência máxima padrão é de 1 hora em uma coleção de trabalhos livre e de 1 minuto em um conjunto de trabalhos padrão. A frequência máxima é configurável em uma coleção de trabalhos que seja menor que o máximo. Todos os trabalhos na coleção de trabalhos são limitados ao valor definido na coleção de trabalhos. Se você tentar criar um trabalho com uma frequência superior à frequência máxima na coleção de trabalhos a solicitação falhará com um código de status 409 Conflito. |
| **Trabalhos** |A cota máxima padrão é de 5 trabalhos em uma coleção de trabalhos livre e 50 trabalhos em uma coleção de trabalhos padrão. O número máximo de trabalhos é configurável em uma coleção de trabalhos. Todos os trabalhos na coleção de trabalhos são limitados ao valor definido na coleção de trabalhos. Se você tentar criar mais trabalhos do que a cota máxima de trabalhos, a solicitação falhará com um código de status 409 Conflito. |
| **Coleções de trabalho** |O número máximo de coleções de trabalho por assinatura é de 200,000. |
| **Retenção de histórico de trabalho** |O histórico de trabalho é retido por até dois meses ou até as últimas 1000 execuções. |
| **Retenção de trabalhos concluídos e com falha** |Os trabalhos concluídos e com falha são armazenados por 60 dias. |
| **Tempo limite** |Há um tempo limite da solicitação (não configurável) estático de 60 segundos para ações de HTTP. Para operações mais demoradas em execução, execute protocolos assíncronos HTTP; por exemplo, retornar um 202 imediatamente, mas continuar a trabalhar no plano de fundo. |

