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
write.csv(dados, "dados_profissionais.csv", row.names = FALSE)
```
