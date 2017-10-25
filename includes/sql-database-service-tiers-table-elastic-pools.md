<!--
Used in:
sql-database-elastic-pool.md 
-->

 
### <a name="basic-elastic-pool-limits"></a>Limites de pool elástico Básico

| eDTUs por pool | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Armazenamento incluído por pool (GB) | 5 | 10 | 20 | 29 | 11,8 | 78 | 117 | 156 |
| Opções de máximo de armazenamento por pool (GB) | 5 | 10 | 20 | 29 | 11,8 | 78 | 117 | 156 |
| Armazenamento máximo OLTP na memória por pool (GB) | N/D | N/D | N/D | N/D | N/D | N/D | N/D | N/D |
| Número máximo de BDs por pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Máximo de trabalhos simultâneos (solicitações) por pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Máximo de logons simultâneos por pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Opções de mínimo de eDTUs por banco de dados | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Opções de máximo de eDTUs por banco de dados | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Armazenamento máximo por banco de dados (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Limites de pool elástico Standard

| eDTUs por pool | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Armazenamento incluído por pool (GB) | 50 | 100 | 200 | 300 | 400 | 800 | 
| Opções de espaço de armazenamento máximo por pool (GB)* | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 | 
| Armazenamento máximo OLTP na memória por pool (GB) | N/D | N/D | N/D | N/D | N/D | N/D | 
| Número máximo de BDs por pool | 100 | 200 | 500 | 500 | 500 | 500 | 
| Máximo de trabalhos simultâneos (solicitações) por pool | 100 | 200 | 400 | 600 | 800 | 1600 |
| Máximo de logons simultâneos por pool | 100 | 200 | 400 | 600 | 800 | 1600 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Opções de mínimo de eDTUs por banco de dados** | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Opções de máximo de eDTUs por banco de dados** | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Armazenamento máximo por banco de dados (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limites de pool elástico Standard (continuação) 

| eDTUs por pool | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Armazenamento incluído por pool (GB) | 1.200 | 1600 | 2000 | 2500 | 3000 | 
| Opções de espaço de armazenamento máximo por pool (GB)* | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Armazenamento máximo OLTP na memória por pool (GB) | N/D | N/D | N/D | N/D | N/D | 
| Número máximo de BDs por pool | 500 | 500 | 500 | 500 | 500 | 
| Máximo de trabalhos simultâneos (solicitações) por pool | 2400 | 3200 | 4000 | 5.000 | 6000 |
| Máximo de logons simultâneos por pool | 2400 | 3200 | 4000 | 5.000 | 6000 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Opções de mínimo de eDTUs por banco de dados** | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Opções de máximo de eDTUs por banco de dados** | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Opções de máximo de armazenamento por banco de dados (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Limites de pool elástico Premium

| eDTUs por pool | **125** | **250** | **500** | **1000** | **1500**| 
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento incluído por pool (GB) | 250 | 500 | 750 | 1024 | 1536 | 
| Opções de espaço de armazenamento máximo por pool (GB)* | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Armazenamento máximo OLTP na memória por pool (GB) | 1 | 2 | 4 | 10 | 12 | 
| Número máximo de BDs por pool | 50 | 100 | 100 | 100 | 100 | 
| Máximo de trabalhos simultâneos por pool (solicitações) | 200 | 400 | 800 | 1600 | 2400 | 
| Máximo de logons simultâneos por pool | 200 | 400 | 800 | 1600 | 2400 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Mínimo de eDTUs por banco de dados | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| Máximo de eDTUs por banco de dados | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Armazenamento máximo por banco de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limites de pool elástico Premium (continuação) 

| eDTUs por pool | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento incluído por pool (GB) | 2.048 | 2560 | 3072 | 3548 | 4096 |
| Opções de espaço de armazenamento máximo por pool (GB)* | 2.048 | 2560 | 3072 | 3548 | 4096|
| Armazenamento máximo OLTP na memória por pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Número máximo de BDs por pool | 100 | 100 | 100 | 100 | 100 | 
| Máximo de trabalhos simultâneos (solicitações) por pool | 3200 | 4000 | 4800 | 5600 | 6400 |
| Máximo de logons simultâneos por pool | 3200 | 4000 | 4800 | 5600 | 6400 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Opções de mínimo de eDTUs por banco de dados | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Opções de máximo de eDTUs por banco de dados | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Armazenamento máximo por banco de dados (GB) | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-rs-elastic-pool-limits"></a>Limites de pool elástico Premium RS

| eDTUs por pool | **125** | **250** | **500** | **1000** |
|:---|---:|---:|---:| ---: | ---: | 
| Armazenamento incluído por pool (GB) | 250 | 500 | 750 | 750 |
| Opções de espaço de armazenamento máximo por pool (GB)* | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 
| Armazenamento máximo OLTP na memória por pool (GB) | 1 | 2 | 4 | 10 |
| Número máximo de BDs por pool | 50 | 100 | 100 | 100 |
| Máximo de trabalhos simultâneos (solicitações) por pool | 200 | 400 | 800 | 1600 |
| Máximo de logons simultâneos por pool | 200 | 400 | 800 | 1600 |
| Máximo de sessões simultâneas por pool | 30000 | 30000 | 30000 | 30000 |
| Opções de mínimo de eDTUs por banco de dados | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 |
| Opções de máximo de eDTUs por banco de dados | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 
| Armazenamento máximo por banco de dados (GB) | 1024 | 1024 | 1024 | 1024 | 
||||||||

> [!IMPORTANT]
> \* Tamanhos de armazenamento maiores que a quantidade de armazenamento incluída estão em versão prévia e aplicam-se custos extras. Para obter detalhes, confira a [página de preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Tamanhos de armazenamento maiores que a quantidade de armazenamento incluída estão em versão prévia e aplicam-se custos extras. Para obter detalhes, confira a [página de preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).
>
> \* Na camada Premium, mais de 1 TB de armazenamento está atualmente disponível nas seguintes regiões: Leste dos EUA 2, Oeste dos EUA, Gov. EUA - Virgínia, Europa Ocidental, Alemanha Central, Sudeste Asiático, Leste do Japão, Leste da Austrália, Canadá Central e Leste do Canadá. 
>
>\*\*O mín/máx de eDTUs por banco de dados, começando em 200 eDTUs e acima em pools **Standard**, está na versão prévia.
>
