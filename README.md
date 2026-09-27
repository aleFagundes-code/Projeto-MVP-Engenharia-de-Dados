# Projeto-MVP-Engenharia-de-Dados
Projeto MVP da Matéria de Engenharia de Dados.

<h1>1 - Contexto de Negócio e Perguntas: </h1>

Para esse projeto levei em consideração uma situação que vivencio durante períodos festivos: Uma prima minha tem um deficiência no coração e para ir a praia e entrar no mar, precisa ser em momento que o mar está calmo. Então decidi encontrar por meio de dados de previsão oceanográficos, qual praias em minha região teriam a menor a quantidade/altura de ondas. Também quais praias tem a menor taxa de turistas em certos períodos. 
Seguindo então a Escala Douglas como regra de negócio (PDF "Escala_Douglas" no repositório)
Os dados brutos, coletos de API gratuitas, trazem:
Altura da Onda: Principal dado avaliativo para Escala Douglas.
Direção da Onda: Direção em que a onda está se movendo. 
Período da Onda: Duração da Onda. 
Período de Pico da Onda: Duração de quanto tempo a onda ficou em sua altura mais alta. 
Data da Previsão: Data para qual a previsão foi feita. 
Latidude e Longitude: Latidude e Longitude da praia.


<h1>2 - Carga dos Dados:</h1>

Os arquivos brutos, coletados em formato .csv (Disponíveis no Repositório), criado o script "ReadingDataFromVolumeToTable", disponível no repositório, para fazer a leitura dos arquivos e separa-los em duas tabelas: Tb_locations e tb_wave_raw_data.

<h1> 3 - Modelagem e Catálogos de Dados: </h1>  

Foi criados três catálogos: Bronze, Silver e Gold

Bronze:

<img width="295" height="145" alt="image" src="https://github.com/user-attachments/assets/a5fd42f3-4d46-4f0f-92fb-1da2d23504ee" />

Catálogo contendo apenas os dados brutos, lidos do arquivo. Nenhuma transformação feita. 

Silver:

<img width="301" height="180" alt="image" src="https://github.com/user-attachments/assets/160baa36-8ded-4214-bdbb-9a10931232e7" />

Catálogo contendo os dados já organizados:
tb_beaches: Tabela contendo as informações da tb_locations, porém organizando os ids para que registro tenha um identificador único. Adicionado também o nome da praia, o campo 'file_id' mantém o id que o registro possui dentro do arquivo. 

tb_wave_data: Tabela contendo os dados coletados, nome das colunas padronizados e cada dado possui um "Beach_ID" para se referência a praia. 

Gold:

<img width="317" height="178" alt="image" src="https://github.com/user-attachments/assets/f38d9097-d152-481a-be54-668860c89477" />

Catálogo contendo os dados já qualificados e categorizados pela Escala Douglas.

tb_wave_data: Tabela contendo os dados coletados e adicionado o campo "aproved" para validar se o dado passou nos testes de qualidade. 

tb_wave_data_scaled: Tabela contendo os dados coletados e categorizados pela Escala Douglas. 

tb_lower_avg_wave_height: Tabela contendo a praia que possui a menor média de onda para um dia específico. 


<h1> 4 - Pipeline de Dados: </h1>

A organização da ETL seguiu o padrão Bronze, Silve e Gold. A principio usei notebooks separados para executar cada passo:

Create TB_BEACHES: Cria a tabela tb_beaches no catálogo silver. 

<img width="944" height="868" alt="image" src="https://github.com/user-attachments/assets/744a48b9-f8f3-4a91-82db-5c1908790ff4" />


Create TB_WAVE_DATA: Cria a tabela tb_beaches no catálogo silver. 

<img width="1350" height="881" alt="image" src="https://github.com/user-attachments/assets/c593ee5c-a27d-4dac-835d-c5311b2e6e74" />


Wave_Data_QUALITY_TEST: Aplica os testes de qualidade nos dados coletados e cria a tabela tb_wave_data no catálogo gold com a informação se o dado foi aprovado ou não.

<img width="1336" height="868" alt="image" src="https://github.com/user-attachments/assets/f970b326-29f5-4e08-954a-4d70a70d159a" />


Douglas_Scale_Wave_Data: Aplica a categorização da escola douglas no dados e cria as tabelas:  tb_wave_data_scaled e tb_lower_avg_wave_height, no catalógo gold, com a informação da categoria da escala douglas. 

<img width="1356" height="866" alt="image" src="https://github.com/user-attachments/assets/c2ae145e-c766-4d85-9278-81b221b4992e" /> (Tb_wave_data_scaled)


<img width="1348" height="865" alt="image" src="https://github.com/user-attachments/assets/58512f79-5110-4f71-a470-6c954b7a542c" /> (tb_lower_avg_wave_height)


Porém, quando tentei criar uma pipeline, por usar alguns comandos SQL direto dentro dos notebooks, a pipeline recusou. Então juntei todos os notebooks em um só, corrigindo o uso dos comandos SQL para usar o spark: 

Pipeline_Wave_Data_NB: Junção dos 4 notebooks, cada um dos notebooks possui seu próprio bloco de código e é executado na ordem correta. 

Todos os scripts citados nesse item, estão disponíveis no repositório. 

<h1> 5 - Qualidade de Dados: </h1>

Por se tratar de uma série temporal de dados coletados, foi aplicado testes de Domínio, Spike e Gradiente sobre os dados, para esses testes os valores limites de cada parâmetro foram coletados na internet (pesquisa rápida no google). Seguindos tais regras:

Spike: Verifica se a média entre os 3 último valores coletados, está entre o do valor minimo e maxímo do limite. Caso não aja dado aprovado anterior, o dado é aprovado. 
Domínio: Verifica se o valor está abaixa do valor máximo do limite. 
Gradiente: Verifica se a diferença entre o dado atual e o último dado aprovado é maior que o limite determinado. Caso não aja dado aprovado, o dado é aprovado. 

Regras aplicadas no script: Wave_Data_QUALITY_TEST

<h1> 6 - Análise de Dados: </h1>

Com os dados coletados é possível verificar, de forma bruta e não precisa, quais praias estão com previsão de estarem com o mar mais calmo e os possíveis dias. Infelizmente, devido a extensão das variáveis que impactam as condições do mar, não é possível determinar com certeza o estado no mar na praia, porém com os dados obtidos já se pode ter uma estimativa. Devido a isso, também não foi possível coletar dados para responder a segunda pergunta. 

<h1> 7 - Autoavaliação: </h1>  

Para fim do projeto, não consegui atingir todos meus objetivos completamente, trabalho com coleta e processamento de dados oceanógrafos, porém sou desenvolvedor. Acreditei que seria capaz de fazer a coleta e analise precisa dos dados de onda para chegar a conclusão que queria e ainda teria como coletar dados sobre a movimentação nas praias, porém não foi possível, a principio tentei determinar o estado mar usando outros parâmetro (swell), porém não consegui avançar, só consegui chegar na utilização da Escala Douglas, por explicação de uma colega Oceanógrafa sobre como é feito esse tipo de análise. 



