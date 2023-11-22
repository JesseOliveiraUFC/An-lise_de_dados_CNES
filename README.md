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

Para construir o processo de ETL usaremos o Pentaho Data Integration. O ETL consiste, basicamente, em coletar os dados trabalhados do arquivo .csv acima, injetar em um banco de dados PostgreSQL e, em seguida, criar um painel de visualização de dados com o Metabase através de consultas em SQL ao Banco de Dados.

Para isso, necessitamos criar as tabelas para receber os dados. A primeira tabela será chamada dadosProfissionais a segunda será dadosMunicipios e elas serão criadas no schema cnes utilizando os códigos a seguir:

```
CREATE TABLE cnes.dadosProfissionais (
    CNES VARCHAR(10),
    CODUFMUN VARCHAR(10),
    CBO VARCHAR(10),
    NOMEPROF VARCHAR(60),
    CNS_PROF VARCHAR(20),
    COMPETENCIA VARCHAR(10)
    );

CREATE TABLE cnes.dadosMunicipios (
    NOMEMUN VARCHAR(30),
    CODUFMUN VARCHAR(10)
    );
```

A figura a seguir ilustra o simplles ETL feito usando o Pentaho:

![image](https://github.com/JesseOliveiraUFC/Analise_de_dados_CNES/assets/97004339/3b12adfe-2057-4e28-be89-2c555303d784)

Uma vez que o processo de ETL foi concluído, o Banco de Dados agora conta com a base atualizada:

![image](https://github.com/JesseOliveiraUFC/Analise_de_dados_CNES/assets/97004339/8cd64568-991e-47f0-a358-125693d383d9)

Próxima etapa será a construção do painel com o Metabase. Mais detalhes a seguir.


## Construção do Painel.


