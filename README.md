

## Models

`dbt run --select <model_name>`

### Modularidade
Partes individuais -> todo
Quebrar o modelo em vários modelos reutilizaveis.

- stg_customers
- stg_orders
- dim_customers

Função **ref** -> permite pegar informação dados de um modelo no dbt, exemplo:
```
select * from {{ ref('stg_customers')}}
```
onde stg_customers é um modelo no dbt.

**Compile** -> mostra o que está acontecendo com o código.
**Linage** -> mostra a execução 
Quando criamos dependências o dbt executa na ordem necessária.

```
dbt docs generate
```

### Name Convention
- Source
- Stagin
  - 1:1 com as tabelas fonte
  - limpeza e padronização
- Intermediários 
  - Entre stagin e as tabelas finais (geralmente escondidas)
  - Sempre construidas sobre os dados das tabelas stagin
- Tabela Fato
- Dimensões
  - pessoas, lugares, etc


### Organizando os projetos
- Pastas
  - stagin
    - sources
  - marts
    - marketing
    - finance
    - etc

### DBT Project.yml

Alterar o arquivo permite configurar os arquivos.
  Exemplo

  ```
models:
  jaffle_shop:
    marts:
      core: 
        +marterialized: table
    stagin:
      +marterialized: view

```
