# Análise_de_dados_CNES
Análise dos dados públicos do CNES sobre profissionais de saúde do estado do Ceará

Neste documento, pretendo detalhar todo o processo da análise dos dados de profissionais de Saúde, desde a coleta dos dados no DataSUS, até a construção do Painel (Dashboard) para visualização dos dados.

## Acesso aos dados no DataSUS e tratamento inicial do arquivo.

A primeira etapa do processo consiste na coleta dos dados no site do governo, o DATASUS. Para isso, só foi necessário acessar o site do governo para solicitar o acesso à competência mais atual disponibilizada (no momento em que esse projeto foi iniciado, em 21/11/2023, a competência mais atual era a de outrubro/2023.
O site para acesso aos dados é público e pode ser acessado em: 

https://datasus.saude.gov.br/transferencia-de-arquivos/#

O download fornecido pelo site vem no formato .dbc (um formato exclusivo do DATASUS) e precisa ser tratado para que possa ser acessado.
A melhor forma de tratar esses arquivos que encontrei foi usando a linguagem R e o aplicativo online R Studio. Usei o seguinte código para realizar a leitura dos dados no formato .dbc e depois convertê-los para .csv:

```
install.packages("read.dbc")
library(read.dbc)
dados <- read.dbc("PFCE2310.dbc")
subset_dados = dados[c("CNES","CODUFMUN","CBO","NOMEPROF","CNS_PROF","COMPETEN")]
write.csv(subset_dados, "dados_profissionais.csv", row.names = FALSE)
```

O Código acima, realiza a instalação da biblioteca necessária para ler o arquivo .dbc e em seguida realiza a leitura dos dados para a variavel "dados".
Como o arquivo possui muitas colunas desnecessárias para a nossa análise, só selecionamos as colunas que julgamos necessárias e, em seguida, salvamos os dados em um arquivo .csv para que pudesse ser lido mais facilmente por outras ferramentas.

Dados sensíveis não serão divulgados, somente utilizados para validação do processo.

Para dar prosseguimento à análise, precisamos de duas tabelas:

1 - Relação dos municípios do Ceará e seus respectivos códigos do IBGE. Essa lista pode ser encontrada no site do IBGE: https://www.ibge.gov.br/explica/codigos-dos-municipios.php#CE

2 - Relação das profissões e seus respectivos CBOs. Um PDF contendo todas as informações pode ser encontrado no site: http://www.mtecbo.gov.br/cbosite/pages/downloads.jsf

Um vez concluída essa etapa, é hora de iniciar o processo de ETL para injetar esses dados em um banco PostgreSQL. Este processo será melhor descrito na seção a seguir:

## Construção do Processo de ETL
