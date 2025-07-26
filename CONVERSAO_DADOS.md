# Conversão de Dados Tasks.csv para Sample_data.csv

Este documento explica como converter os dados do arquivo `tasks.csv` para o formato esperado pelo modelo NESA, seguindo a estrutura do `sample_data.csv`.

## Arquivos Criados

1. **`convert_tasks_to_sample_format.py`** - Script principal de conversão
2. **`notebooks/convert_data.ipynb`** - Notebook demonstrativo do processo
3. **`data/converted_sample_data.csv`** - Arquivo convertido pelo script
4. **`data/converted_sample_data_notebook.csv`** - Arquivo convertido pelo notebook

## Formato de Saída

O arquivo convertido possui exatamente as 12 colunas esperadas pelo modelo NESA:

1. **email_address** - Email do usuário (padrão: "user@email.com")
2. **title** - Título do evento (do campo SUMMARY)
3. **duration_minute** - Duração em minutos (convertido de DURATION em horas)
4. **register_time** - Tempo de registro (campo CREATED)
5. **start_time** - Tempo de início (campo DTSTART)
6. **start_iso_year** - Ano ISO do evento
7. **start_iso_week** - Semana ISO do evento
8. **week_register_sequence** - Sequência de registro dentro da semana
9. **register_start_week_distance** - Distância em semanas entre registro e início
10. **register_start_day_distance** - Distância em dias entre registro e início
11. **is_recurrent** - Se o evento é recorrente (sempre False por padrão)
12. **start_time_slot** - Slot de tempo (0-335, representando slots de 30min em uma semana)

## Como Usar o Script

### Uso Básico
```bash
python convert_tasks_to_sample_format.py
```

### Uso com Parâmetros Personalizados
```bash
python convert_tasks_to_sample_format.py \
    --input ./data/tasks.csv \
    --output ./data/my_converted_data.csv \
    --email my_email@example.com
```

### Parâmetros Disponíveis
- `--input`: Caminho para o arquivo tasks.csv (padrão: ./data/tasks.csv)
- `--output`: Caminho para o arquivo de saída (padrão: ./data/converted_sample_data.csv)
- `--email`: Email address para usar nos dados (padrão: user@email.com)

## Resultados da Conversão

### Estatísticas dos Dados Convertidos
- **Total de eventos**: 4,061 eventos convertidos com sucesso
- **Período**: 2015-12-13 até 2025-07-18
- **Duração média**: 152.1 minutos
- **Time slots únicos**: 189 (de 336 possíveis)
- **Semanas únicas**: 52

### Validação
- ✓ Todas as 12 colunas estão presentes
- ✓ Time slots estão no range válido (0-335)
- ✓ Tipos de dados corretos
- ✓ Mesmo formato do sample_data.csv original

## Algoritmos de Conversão

### Cálculo do Time Slot
```python
weekday = start_dt.weekday()  # 0=Segunda, 6=Domingo
slot_in_day = (hour * 60 + minute) // 30  # Slots de 30 minutos
time_slot = weekday * 48 + slot_in_day  # 48 slots por dia
```

### Cálculo da Distância em Semanas
```python
monday_register = register_dt - timedelta(days=register_dt.weekday())
monday_start = start_dt - timedelta(days=start_dt.weekday())
week_distance = (monday_start - monday_register).days // 7
```

### Sequência de Registro na Semana
- Ordena eventos da mesma semana por tempo de criação
- Atribui sequência baseada na ordem de registro

## Uso no Modelo NESA

Após a conversão, o arquivo pode ser usado diretamente com o modelo NESA:

```bash
python test.py --input_path ./data/converted_sample_data.csv
```

O arquivo convertido mantém a compatibilidade total com o formato esperado pelo modelo, permitindo o treinamento e teste com os dados pessoais do usuário.
