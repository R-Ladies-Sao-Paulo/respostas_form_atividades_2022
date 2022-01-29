
<!-- README.md is generated from README.Rmd. Please edit that file -->

# Respostas ao formulário de atividades

Respostas para o formulário de sugestões de atividade da R-Ladies SP em
2022.

## Importação

``` r
# URL da Google Sheets que contém as respostas
# (Vinculada ao Google Forms)
url <- "https://docs.google.com/spreadsheets/d/1ncSmpBt7WhrYt7rdNxQ6BoirNawrz_58jxef8_G2-v4/"

# Queremos fazer a autenticação usando as chaves em cache
# usando um email autorizado
googlesheets4::gs4_auth(email = "milz.bea@gmail.com")

# Importar a forma bruta das respostas
respostas_bruto <- googlesheets4::read_sheet(url)
#> ✓ Reading from "Sugestão de atividades - R-Ladies SP (respostas)".
#> ✓ Range 'Respostas ao formulário 1'.
```

## Organização da base

``` r
# quais são as colunas que temos?
names(respostas_bruto)
#> [1] "Carimbo de data/hora"                                                     
#> [2] "Quais tipos de atividade você prefere?"                                   
#> [3] "Quais temas você gostaria que fossem abordados em eventos? Escolha até 5."
#> [4] "Em quais dias e horários você prefere que as atividades aconteçam?"       
#> [5] "Gostaria de oferecer mais alguma sugestão?"                               
#> [6] "Você gostaria de palestrar sobre algum assunto?"                          
#> [7] "Qual tema você gostaria de palestrar?"                                    
#> [8] "Qual é o seu nome?"                                                       
#> [9] "Qual é o seu email?"

respostas <- respostas_bruto |>
  # limpar o nome das variáveis:
  janitor::clean_names() |>
  # o transmute funciona como a junção de um select, rename, e mutate!
  dplyr::transmute(
    carimbo_de_data_hora,
    preferencia_atividade = quais_tipos_de_atividade_voce_prefere,
    temas = quais_temas_voce_gostaria_que_fossem_abordados_em_eventos_escolha_ate_5,
    preferencia_dia_horario = em_quais_dias_e_horarios_voce_prefere_que_as_atividades_acontecam,
  )

# ver como ficou a base
dplyr::glimpse(respostas)
#> Rows: 25
#> Columns: 4
#> $ carimbo_de_data_hora    <dttm> 2022-01-25 08:59:37, 2022-01-25 09:19:06, 202…
#> $ preferencia_atividade   <chr> "Apresentação (com conteúdo expositivo), Works…
#> $ temas                   <chr> "Manipulação de dados (pacote dplyr), Séries t…
#> $ preferencia_dia_horario <chr> "Durante a semana, no período noturno", "Duran…
```

## Temas

``` r
respostas |>
  # selecionar apenas a coluna tema
  dplyr::select(temas) |>
  # separar a coluna tema, a cada vírgula

  tidyr::separate(temas,
    into = paste0("tema_", 1:20),
    sep = ",",
    fill = "right"
  ) |>
  # transformamos a base para o formato longo
  tidyr::pivot_longer(
    cols = tidyselect::everything(),
    values_drop_na = TRUE,
    values_to = "tema"
  ) |>
  # remove a coluna name, criada na etapa anterior
  dplyr::select(-name) |>
  # remover os espaços extras
  dplyr::mutate(tema = stringr::str_trim(tema)) |>
  # contar quantas vezes cada tema apareceu
  # e ordenar de forma decrescente
  dplyr::count(tema, sort = TRUE) |>
  # gerar uma tabela
  knitr::kable(col.names = c("Tema", "Quantidade de respostas"))
```

| Tema                                         | Quantidade de respostas |
|:---------------------------------------------|------------------------:|
| Git e GitHub                                 |                      12 |
| Séries temporais                             |                      12 |
| Dashboards com Shiny                         |                      11 |
| Relatórios com o pacote RMarkdown            |                      11 |
| Manipulação de dados (pacote dplyr)          |                      10 |
| Modelagem Supervisionada (pacote tidymodels) |                      10 |
| Visualização de dados (pacote ggplot2)       |                      10 |
| Mapas com R e geom_sf()                      |                       9 |
| Web Scraping                                 |                       9 |
| Acessando APIs                               |                       7 |
| Tratamento de erros (pacote purrr)           |                       7 |
| Arrumação de dados (pacote tidyr)            |                       6 |
| Criação funções                              |                       6 |
| Iteração (pacote purrr)                      |                       6 |
| Modelagem de textos (NLP)                    |                       6 |
| Análise descritiva de textos                 |                       5 |
| GitHub Actions                               |                       5 |
| Introdução ao R                              |                       5 |
| Processamento paralelo (pacote furrr)        |                       5 |
| Criação de pacotes                           |                       4 |
| Pacote data.table                            |                       4 |
| Apresentações com o pacote xaringan          |                       3 |
| Dataprep com o pacote recipes                |                       1 |
| Funções estatísticas                         |                       1 |
| Grafos e Análise de redes sociais            |                       1 |
| Testes unitários (pacote testthat)           |                       1 |

## Tipo de atividade

``` r
# O código é similar ao código para temas
respostas |>
  dplyr::select(preferencia_atividade) |>
  tidyr::separate(preferencia_atividade, into = paste0("atividade_", 1:5), sep = ",", fill = "right") |>
  tidyr::pivot_longer(cols = tidyselect::everything(), values_drop_na = TRUE, values_to = "atividade") |>
  dplyr::select(-name) |>
  dplyr::mutate(atividade = stringr::str_trim(atividade)) |>
  dplyr::count(atividade, sort = TRUE) |>
  knitr::kable(col.names = c("Atividade", "Quantidade de respostas"))
```

| Atividade                                                                        | Quantidade de respostas |
|:---------------------------------------------------------------------------------|------------------------:|
| Workshop (atividade que tem conteúdo expositivo e conteúdo prático)              |                      20 |
| Apresentação focada em como usar um pacote                                       |                      14 |
| Apresentação (com conteúdo expositivo)                                           |                      12 |
| Mesa redonda (conversa sobre algum tema com pessoas convidadas)                  |                       5 |
| Evento com mais de uma apresentação curta do mesmo tema                          |                       4 |
| Evento mensal com uma convidada de fora para falar sobre algum tema relacionado. |                       1 |

## Dia/horário de preferência

``` r
# O código é similar ao código para temas
respostas |>
  dplyr::select(preferencia_dia_horario) |>
  # Aqui rolou uma inconsistência dentre as opções de resposta,
  # então foi necessário padronizar
  dplyr::mutate(
    preferencia_dia_horario = stringr::str_replace_all(
      preferencia_dia_horario,
      "Durante a semana, no período noturno",
      "Durante a semana - noite"
    )
  ) |>
  tidyr::separate(
    preferencia_dia_horario,
    into = paste0("diahora_", 1:10), sep = ",", fill = "right"
  ) |>
  tidyr::pivot_longer(cols = tidyselect::everything(), values_drop_na = TRUE, values_to = "diahora") |>
  dplyr::select(-name) |>
  dplyr::mutate(diahora = stringr::str_trim(diahora)) |>
  dplyr::count(diahora, sort = TRUE) |>
  knitr::kable(col.names = c("Dia e horário", "Quantidade de respostas"))
```

| Dia e horário            | Quantidade de respostas |
|:-------------------------|------------------------:|
| Durante a semana - noite |                      18 |
| Sábado - manhã           |                      13 |
| Sábado - tarde           |                      10 |
| Domingo - manhã          |                       6 |
| Domingo - noite          |                       6 |
| Domingo - tarde          |                       6 |
| Sábado - noite           |                       4 |
