[![Live Demo](https://img.shields.io/badge/Live%20Demo-abs--pyrowatch.streamlit.app-FF4B4B?logo=streamlit&logoColor=white)](https://abs-pyrowatch.streamlit.app)

# `PyroWatch: Dashboard de Monitoramento de Queimadas`

> Plataforma de monitoramento de queimadas por satelite com modelo preditivo de risco baseado em features climaticas e historicas. Streamlit + Plotly, arquitetura modular com 7 modulos, 4 abas de analise. Global Solution 2026.1, FIAP.

---

## `Tecnologias`

![Python](https://img.shields.io/badge/Python-3.10+-blue)
![Streamlit](https://img.shields.io/badge/Streamlit-1.35+-ff4b4b)
![Plotly](https://img.shields.io/badge/Plotly-5.20+-purple)
![NumPy](https://img.shields.io/badge/NumPy-modelo%20preditivo-013243)
![Pandas](https://img.shields.io/badge/pandas-2.0+-green)
![License](https://img.shields.io/badge/license-MIT-green)

---

## `O que faz`

Monitora focos de queimadas em 8 estados brasileiros com dados satelitais (INPE-style), exibe mapas de calor geograficos, emite alertas por bioma/estado e projeta o risco dos proximos 14 dias com um modelo linear calibrado em dados historicos 2019-2024. O modelo e implementado 100% com NumPy, sem dependencia de scikit-learn.

---

## `Arquitetura`

```
app.py
    |
    +-- providers/
    |       fire_provider.py        fetch_fire_data(), fetch_climate_data() com cache @st.cache_data
    |
    +-- pipelines/
    |       fire_pipeline.py        filter_fire_data(): filtros de estado, bioma e nivel de risco
    |
    +-- features/
    |       overview.py             aba Visao Geral: KPIs, series temporais, distribuicoes
    |       fire_monitor.py         aba Mapa e Risco: scatter geo + heatmap
    |       alerts.py               aba Central de Alertas: ranking de criticidade
    |       ai_forecast.py          aba Previsao IA: projecao 3-14 dias com intervalo de confianca
    |
    +-- models/
    |       risk_predictor.py       predict_risk(), get_feature_importance(), regressao linear NumPy
    |
    +-- ui/
    |       components.py           metric_card(), risk_badge(), loading_spinner()
    |       charts.py               graficos Plotly reutilizaveis
    |       sidebar.py              filtros: periodo, estados, biomas, nivel de risco
    |
    +-- state/
            session_state.py        init_state(), get(), set() para st.session_state tipado
```

---

## `Modelo Preditivo de Risco`

Regressao linear implementada com NumPy puro (sem sklearn). Features e pesos:

| Feature | Peso | Descricao |
|---------|------|-----------|
| Historico de Focos | 45% | Frequencia media de focos no periodo selecionado |
| Anomalia de Temperatura | 25% | Temperatura acima da media historica regional |
| Deficit de Umidade | 20% | Quanto a umidade esta abaixo do limiar critico (60%) |
| Fator Vento | 10% | Velocidade do vento normalizada (dispersao de focos) |

Limiares de classificacao:

| Score | Nivel |
|-------|-------|
| >= 0.75 | Critico |
| >= 0.50 | Alto |
| >= 0.25 | Moderado |
| < 0.25 | Baixo |

Intervalo de confianca: 85% no dia 1, decaindo 2% por dia de horizonte.

---

## `Interface`

| Aba | Conteudo |
|-----|---------|
| Visao Geral | KPIs (total focos, area media, FRP medio, bioma mais afetado) + serie temporal + distribuicao por estado |
| Mapa e Risco | Scatter geo com Fire Radiative Power (FRP) + heatmap de densidade |
| Central de Alertas | Ranking de estados por criticidade + alertas por bioma + tabela completa filtrada |
| Previsao IA | Score de risco previsto (3-14 dias) + intervalo de confianca (85%) + importancia das features |

Sidebar: filtros de periodo, estados, biomas e nivel de risco aplicados globalmente a todas as abas.

---

## `Estrutura`

```
pyrowatch/
├── app.py
├── features/
│   ├── ai_forecast.py
│   ├── alerts.py
│   ├── fire_monitor.py
│   └── overview.py
├── models/
│   └── risk_predictor.py
├── pipelines/
│   └── fire_pipeline.py
├── providers/
│   └── fire_provider.py
├── state/
│   └── session_state.py
├── ui/
│   ├── charts.py
│   ├── components.py
│   └── sidebar.py
├── requirements.txt
└── README.md
```

---

## `Instalacao`

```bash
git clone https://github.com/Arthur-Baptista-dos-Santos/pyrowatch.git
cd pyrowatch

python -m venv .venv
.venv\Scripts\activate       # Windows
# source .venv/bin/activate  # Linux/Mac

pip install -r requirements.txt
streamlit run app.py
```

Acesse `http://localhost:8501`.

---

## `Conceitos aplicados`

- **`Streamlit multipage com tabs`**: 4 abas independentes com estado compartilhado via `session_state`
- **`Cache de dados`**: `@st.cache_data(ttl=300)` evita re-fetch a cada interacao do usuario
- **`Regressao linear sem sklearn`**: modelo implementado com NumPy puro para minima dependencia
- **`Plotly dark theme`**: graficos interativos com `template="plotly_dark"` e `paper_bgcolor` customizado
- **`Modularizacao por responsabilidade`**: cada modulo tem uma unica responsabilidade (providers buscam, pipelines filtram, features renderizam, ui exibe)
- **`FRP (Fire Radiative Power)`**: metrica satelital INPE para intensidade de focos em MW

---

## `Licenca`

Distribuido sob a licenca MIT. Veja [LICENSE](LICENSE) para mais informacoes.

---

## `Autor`

**Arthur Baptista dos Santos**
RM 565346 · Inteligencia Artificial · FIAP 2025-2026

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Arthur%20Baptista-0077B5?logo=linkedin)](https://linkedin.com/in/arthur-baptista-dos-santos)
[![GitHub](https://img.shields.io/badge/GitHub-Arthur--Baptista--dos--Santos-181717?logo=github)](https://github.com/Arthur-Baptista-dos-Santos)
