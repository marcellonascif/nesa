# NESA: Neural Event Scheduling Assistant

NESA (Neural Event Scheduling Assistant) é um assistente de agendamento de eventos baseado em deep learning que pode recomendar os horários mais adequados para novos eventos de calendário de cada usuário. Este repositório fornece a implementação oficial do NESA, incluindo pré-processamento de dados, treinamento e inferência do modelo.

## 📋 Sobre o Projeto

O NESA utiliza uma arquitetura de deep learning que combina:
- **Embeddings de contexto**: Representações de usuário, duração e slots temporais
- **Processamento de texto**: Análise de títulos de eventos usando CNNs e LSTMs
- **Contexto temporal**: Análise de eventos anteriores para entender padrões do usuário
- **Recomendação inteligente**: Predição de slots temporais mais apropriados


## 📊 Métricas de Avaliação

O modelo é avaliado usando:
- **Recall@1**: Acurácia top-1 das recomendações
- **Recall@5**: Acurácia top-5 das recomendações
- **MRR (Mean Reciprocal Rank)**: Ranking médio recíproco
- **IEUC**: Métrica específica para avaliação de agendamento

## 🔗 Citação

```bibtex
@inproceedings{kim2018learning,
  title={Learning User Preferences and Understanding Calendar Contexts for Event Scheduling},
  author={Kim, Donghyeon and Lee, Jinhyuk and Choi, Donghee and Choi, Jaehoon and Kang, Jaewoo},
  booktitle={Proceedings of the 27th ACM International Conference on Information and Knowledge Management},
  pages={337--346},
  year={2018},
  organization={ACM}
}
```

**Paper**: [Learning User Preferences and Understanding Calendar Contexts for Event Scheduling](https://arxiv.org/abs/1809.01316)

## ⚙️ Pré-requisitos

### Ambiente de Sistema
- **Python 3.7+** (testado com Python 3.11)
- **PyTorch 1.13.0+**
- **Git LFS** (para arquivos grandes)

### Hardware (Opcional)
- **GPU NVIDIA** (8GB+ VRAM recomendado)
- **CUDA** e **cuDNN** (para aceleração GPU)

## 🚀 Instalação e Setup Completo

### 1. Clone do Repositório
```bash
git clone <repository-url>
cd nesa
```

### 2. Configuração do Ambiente Python
```bash
# Criar ambiente virtual (recomendado)
python -m venv nesa-env
source nesa-env/bin/activate  # Linux/Mac
# ou
nesa-env\Scripts\activate     # Windows

# Instalar dependências
pip install -r requirements.txt
```

### 3. Download dos Word Embeddings
```bash
# Criar diretório para embeddings
mkdir -p nlp

# Download do GloVe (atenção: arquivo de ~2.03GB)
cd nlp
wget http://nlp.stanford.edu/data/glove.840B.300d.zip
unzip glove.840B.300d.zip
cd ..
```

### 4. Configuração da Google Calendar API (Opcional)

Para usar seus próprios dados de calendário:

1. **Acesse o [Google Cloud Console](https://console.cloud.google.com/)**
2. **Crie um novo projeto ou selecione um existente**
3. **Ative a Google Calendar API**
4. **Crie credenciais OAuth 2.0**
5. **Baixe o arquivo `client_secret.json`** para o diretório raiz do projeto
6. **Modifique o arquivo `get_google_calendar_events.py`**:
   ```python
   CLIENT_SECRET_FILE = 'client_secret.json'  # Seu arquivo de credenciais
   ```

## 📈 Execução e Reprodutibilidade

### Teste com Dados de Exemplo
```bash
# Configurar PYTHONPATH
export PYTHONPATH=$PYTHONPATH:$(pwd)

# Executar teste com dados de exemplo
python test.py
```


2. **Executar modelo com seus dados:**
```bash
python test.py --input_path ./data/<seu_csv>.csv
```

### Parâmetros de Linha de Comando

```bash
python test.py \
    --input_path "./data/sample_data.csv" \          # Arquivo CSV de entrada
    --serialized_data_path "./data/preprocess_test.pkl" \  # Cache de pré-processamento
    --model_path "./data/nesa_180522_0.pth" \        # Modelo pré-treinado
    --trained_dict_path "./data/dataset_180522_dict.pkl" \  # Dicionário treinado
    --seed 3 \                                       # Seed para reprodutibilidade
    --yes_cuda 1                                     # Usar GPU (1) ou CPU (0)
```

## 📁 Estrutura de Dados

### Formato CSV de Entrada
Os eventos devem estar no formato CSV com 12 colunas:
```
email_address,title,duration_minute,register_time,start_time,start_iso_year,start_iso_week,week_register_sequence,register_start_week_distance,register_start_day_distance,is_recurrent,start_time_slot
```

**Exemplo:**
```csv
user@email.com,Meeting with team,60,2023-08-10 09:00:00+00:00,2023-08-15 14:00:00+09:00,2023,33,0,0,5,False,284
```

### Slots Temporais
- **Range**: 0-335 (total de 336 slots)
- **Resolução**: 30 minutos por slot
- **Cobertura**: 7 dias × 48 slots/dia = 336 slots/semana

## 🎯 Exemplo Prático de Execução

### Execução Passo-a-Passo

```bash
# 1. Preparação do ambiente
export PYTHONPATH=$PYTHONPATH:$(pwd)
```

## 🔧 Estrutura do Projeto

```
nesa/
├── data/                          # Dados e modelos
│   ├── sample_data.csv           # Dados de exemplo
│   ├── nesa_180522_0.pth         # Modelo pré-treinado
│   ├── dataset_180522_dict.pkl   # Dicionário de vocabulário
│   └── converted_data.csv        # Dados convertidos
├── nlp/                          # Word embeddings
│   └── glove.840B.300d.txt      # GloVe embeddings
├── model.py                      # Arquitetura do modelo NESA
├── dataset.py                    # Processamento e carregamento de dados
├── test.py                       # Script de avaliação
├── get_google_calendar_events.py # Extração de dados do Google Calendar
├── utils.py                      # Utilitários diversos
└── requirements.txt              # Dependências Python
```

## 📄 Licença

Este projeto está licenciado sob a licença especificada no arquivo `LICENSE`.

## License
Apache License 2.0
