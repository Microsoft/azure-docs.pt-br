
> [!NOTE] 
> As visualizações são disponibilizadas a você se concordar com os termos de uso. Para obter mais informações, consulte [Termos de Uso Complementares do Microsoft Azure para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> Essa versão prévia está limitada às seguintes regiões:
> - Oeste dos EUA 2
> - Leste dos EUA
> - Europa Ocidental
> - Sudeste do Pacífico Asiático


A família de VMs da série B permite que você escolha o tamanho de VM que oferece o nível necessário da linha de base de desempenho para sua carga de trabalho, com a capacidade de aumentar o desempenho da CPU em até 100% de um Intel® Broadwell E5-2673 v4 2.3GHz ou vCPU do processador do Intel® Haswell 2.4 GHz E5-2673 v3.

As VMs da série B são ideais para cargas de trabalho que não precisam ter o desempenho total da CPU continuamente, como servidores Web, bancos de dados pequenos e ambientes de desenvolvimento e teste. Normalmente, essas cargas de trabalho têm requisitos de desempenho expansíveis. A série B permite a compra de um tamanho de VM com a linha de base de desempenho, e a instância da VM criará créditos quando estiver usando menos que a linha de base. Quando a VM acumular crédito, ela poderá ultrapassar a linha de base usando até 100% da vCPU quando seu aplicativo exigir um melhor desempenho de CPU.

A série B tem seis tamanhos de VM:

| Tamanho          | da vCPU | Memória: GiB | SSD local: GiB | Base de desempenho da CPU da VM | Máximo desempenho da CPU da VM | Créditos armazenados/hora | Máximo de créditos armazenados |
|---------------|--------|-------------|----------------|--------------------------------|---------------------------|-----------------------|--------------------|
| Standard_B1s  | 1      | 1           | 4              | 10%                            | 100%                      | 6                     | 144                |
| Standard_B1ms | 1      | 2           | 4              | 20%                            | 100%                      | 12                    | 288                |
| Standard_B2s  | 2      | 4           | 8              | 40%                            | 200%                      | 24                    | 576                |
| Standard_B2ms | 2      | 8           | 16             | 60%                            | 200%                      | 36                    | 864                |
| Standard_B4ms | 4      | 16          | 32             | 90%                            | 400%                      | 54                    | 1296               |
| Standard_B8ms | 8      | 32          | 64             | 135%                           | 800%                      | 81                    | 1944               |




## <a name="q--a-about-this-preview"></a>Perguntas e respostas sobre esta versão prévia

### <a name="q-how-can-i-participate-in-this-preview"></a>P: como posso participar desta versão prévia?
**R**: solicite uma cota para a série B em uma das regiões com suporte.  Depois que a cota for aprovada, você poderá usar o portal ou as APIs para fazer sua implantação como sempre. Para saber mais, confira [Solicitações de aumento da cota de núcleo do Gerenciador de Recursos](../articles/azure-supportability/resource-manager-core-quotas-request.md).

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>P: como obter 135% da linha de base de desempenho de uma VM?
**R**: os 135% são compartilhados entre as 8 vCPUs que compõem o tamanho da VM. Por exemplo, se seu aplicativo utiliza 4 dos 8 núcleos trabalhando em processamento de lotes e cada uma das 4 vCPUs estão sendo executadas a 30% da utilização, o desempenho total da CPU da VM será de 120%.  Isso significa que a VM estaria criando créditos de tempo com base no delta de 15% da sua linha de base de desempenho.  Mas isso também significa que quando você tem créditos disponíveis, a mesma VM pode usar 100% de todas as 8 vCPUs, o que daria à VM um desempenho máximo de CPU de 800%.

### <a name="q-is-there-a-discount-on-price-during-the-preview"></a>P: há desconto no preço durante a versão prévia?
**R**: sim, os preços da versão prévia podem ser vistos na nossa [página de preços](http://aka.ms/vmsizes).

### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>P: como posso monitorar meu saldo e consumo?
**R**: apresentaremos duas novas métricas nas próximas semanas. A métrica **Credit** permitirá ver quantos créditos a sua VM acumulou e a métrica**ConsumedCredit** mostrará quantos créditos de CPU sua VM consumiu do banco.    Você poderá exibir essas métricas no painel de métricas no portal ou programaticamente pelas APIs do Azure Monitor.

Para saber mais sobre como acessar os dados de métrica do Azure, confira [Visão geral das métricas no Microsoft Azure](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>P: como os créditos são acumulados?
**R**: as taxas de consumo e acumulação da VM estão definidas de modo que uma VM em execução na sua linha de base de desempenho não terá acúmulo ou consumo de créditos.  Uma VM terá um aumento de créditos sempre que estiver em execução abaixo da linha de base de desempenho e terá uma redução nos créditos sempre que a VM estiver usando a CPU acima da sua linha de base de desempenho.

**Exemplo**: implantei uma VM usando o tamanho B1ms para meu aplicativo de banco de dados pequeno. Esse tamanho permite que o meu aplicativo use até 20% de uma vCPU como minha linha de base, o que significa 0,2 de crédito por minuto que posso usar ou acumular. 

Meu aplicativo está ocupado no início e no final do dia de trabalho dos meus funcionários, de 7h às 9h e de 16h às 18h. Durante as outras 20 horas do dia, meu aplicativo está normalmente ocioso, usando apenas 10% da vCPU. No horário fora de pico, acumulo 0,2 de crédito por minuto, mas consumo 0,1 de crédito por minuto, ou seja, minha VM acumulará 0,1 x 60 = 6 créditos por hora.  Nas 20 horas em que estou fora de pico, acumularei 120 créditos.  

Durante o horário de pico, meu aplicativo aproveita 60% de utilização de vCPU, ainda acumulo 0,2 de crédito por minuto, mas consumo 0,6 de crédito por minuto, com um custo de 0,4 de crédito por minuto, ou 0,4 x 60 = 24 créditos por hora. Tenho 4 horas por dia de pico, ou seja, meu uso em hora de pico é de 4 x 24 = 96 créditos.

Se eu usar os 120 créditos acumulados fora de pico e subtrair os 96 créditos que usei para meu horário de pico, acumulo mais 24 créditos por dia para usar em outras atividades.


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>P: a série B dá suporte a discos de dados de Armazenamento Premium?
**R**: sim, todos os tamanhos da série B dão suporte a discos de dados de Armazenamento Premium.   
    

### <a name="q-which-regions-can-i-access-the-preview-from"></a>P: quais regiões permitem o acesso à versão prévia?
**R**: a versão prévia da série B estará disponível nas seguintes regiões:
- Oeste dos EUA 2
- Leste dos EUA
- Europa Ocidental
- Sudeste do Pacífico Asiático

Após a conclusão da versão prévia, lançaremos a série B para todas as demais regiões.
    

    
