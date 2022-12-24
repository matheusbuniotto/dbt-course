

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

## Sources
Dados fonte (**raw**) -> sources
Configurar as fontes uma única vez em um arquivo .yml
Visualizar a lineage

```
sources:
  - name: jaffle_shop
    database: raw
    schema: jaffle_shop
    tables:
      - name: customers
      - name: orders
```

```
with source as (

    select * from {{ source('jaffle_shop', 'customers') }}
),
```

### Source Freshness
Adicionamos um bloco para examinar as fontes no arquivo.yml
```
        loaded_at_field: _etl_loaded_at (timestamp do horário do processamento)
        freshness:
          warn_after: {count: 12, period: hour}
          error_after: {count: 24, period: hour}
```
Verificar com:\
`dbt source freshness`

## Testing
DBT Testes padrões:
.yml
 
- unique
- not null
- accepeted_values
- relationships

**dbt Commands**
```
Execute dbt test to run all generic and singular tests in your project.
Execute dbt test --select test_type:generic to run only generic tests in your project.
Execute dbt test --select test_type:singular to run only singular tests in your project.
```


Exemplo
version: 2

```dbt
models:
  - name: stg_customers
    columns: 
      - name: customer_id
        tests:
          - unique
          - not_null

  - name: stg_orders
    columns:
      - name: order_id
        tests:
          - unique
          - not_null
      - name: status
        tests:
          - accepted_values:
              values:
                - completed
                - shipped
                - returned
                - return_pending
                - placed
```
executar com:\
`dbt test --select <model_name>`

### Testes Singulares/Personalizados
- Uma query em .sql é usada como teste
- A query deve detectar os casos onde deve ser alarmado
  

### Testando Fontes
```sql
sources:
  - name: jaffle_shop
    database: dbt-tutorial
    schema: jaffle_shop
    tables:
      - name: customers
        columns:
          - name: id
            tests:
              - unique
              - not_null
      - name: orders
        columns:
          - name: id
            tests:
              - unique
              - not_null
      
        loaded_at_field: _etl_loaded_at
        freshness:
          warn_after: {count: 12, period: hour}
          error_after: {count: 24, period: hour}
         
```

dbt test --select source:<source_name>

## Documentação
